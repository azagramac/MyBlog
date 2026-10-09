---
description: >-
  Activar Secure Boot en la placa base, restaurar las claves de fábrica de UEFI
  y mantener el arranque de Debian con un kernel compilado personalmente.
  Además, permitir la carga de módulos externos.
---

# Secure Boot en Debian 13 con kernel personalizado

**Sistema operativo:** Debian 13.7 (Trixie)\
**Placa base:** Gigabyte X570 AORUS ELITE, revisión 1.0\
**BIOS:** F40\
**Kernel personalizado:** `6.19.14-ryzen9`

***

### 1. 🔐 Objetivo

Activar Secure Boot en la placa base, restaurar las claves de fábrica de UEFI y mantener el arranque de Debian con un kernel compilado personalmente. Además, permitir la carga de módulos externos, en particular los de VirtualBox, cuando Secure Boot está habilitado.

El proceso requiere distinguir tres elementos:

1. **Claves de Secure Boot de UEFI:** las administra el firmware de la placa base y determinan qué componentes EFI se consideran confiables durante el arranque.
2. **Certificado MOK (Machine Owner Key):** una clave propia que se incorpora a la cadena de confianza gestionada por `shim` de Debian y permite validar componentes firmados con ella.
3. **Firmas de kernel y módulos:** la imagen del kernel y los módulos externos deben estar firmados y sus firmas deben validarse mediante una clave de confianza aceptada por el sistema.

El resultado conseguido es que Secure Boot permanece habilitado, el kernel personalizado está firmado y arranca, y los tres módulos de VirtualBox (`vboxdrv`, `vboxnetflt` y `vboxnetadp`) tienen la firma propia y se cargan correctamente.

***

### 2. 📚 Conceptos fundamentales

#### 2.1. 🛡️ ¿Qué hace Secure Boot?

Secure Boot es una función de UEFI que verifica las firmas de los componentes del proceso de arranque. Su objetivo es impedir que se ejecute código de arranque que no esté autorizado por las claves de confianza configuradas.

En una instalación Debian que utiliza `shim`, la cadena habitual es:

1. El firmware UEFI valida el cargador de arranque firmado.
2. `shim` verifica el siguiente componente, normalmente GRUB, conforme a su política de confianza.
3. GRUB carga el kernel, que debe superar las comprobaciones de firma aplicables.
4. Una vez arrancado el kernel, el sistema valida los módulos del kernel conforme a la configuración y a las claves de confianza disponibles.

**Importante:** Secure Boot no cifra el disco ni sustituye a otros mecanismos de seguridad. Tampoco significa que todo programa ejecutado en Debian deba estar firmado; las comprobaciones descritas afectan especialmente a la cadena de arranque y a la carga de código del kernel.

#### 2.2. 🔑 ¿Qué son las claves de UEFI?

La base de datos de Secure Boot utiliza varias variables de claves:

| Variable                 | Función                                                                                             |
| ------------------------ | --------------------------------------------------------------------------------------------------- |
| `PK` — Platform Key      | Establece la autoridad que controla la plataforma Secure Boot y los cambios de las bases de claves. |
| `KEK` — Key Exchange Key | Autoriza actualizaciones de las bases de datos de firmas y revocaciones.                            |
| `db`                     | Contiene certificados, claves o hashes autorizados para validar componentes EFI.                    |
| `dbx`                    | Contiene certificados, claves o hashes revocados que no deben aceptarse.                            |

La implementación exacta depende del firmware y de las entradas instaladas. Las bases `db` y `dbx` no son equivalentes: una autoriza y la otra revoca.

#### 2.3. 🏭 ¿Qué significa _Restore Factory Keys_?

La opción **Restore Factory Keys** restaura las claves y bases de datos de Secure Boot proporcionadas por el fabricante o la configuración de fábrica de la placa base. Su contenido exacto depende de la implementación del firmware.

En términos generales, esta operación permite recuperar la configuración estándar de confianza de UEFI, que puede incluir claves de Microsoft y claves del fabricante.

Sus consecuencias pueden ser:

* Restaurar la Platform Key (`PK`) y las bases `KEK`, `db` y `dbx` a sus valores de fábrica.
* Sacar al firmware del estado _Setup Mode_ cuando se instala una `PK`, pasando a un estado de gestión de claves.
* Permitir que Secure Boot se habilite con las claves de confianza de fábrica.
* Modificar las claves que el firmware acepta para validar cargadores y ejecutables EFI.

**No es lo mismo que registrar una MOK personal.** La MOK propia se gestiona mediante el mecanismo de `shim` y la herramienta MOK Manager. Restaurar las claves de fábrica de UEFI no equivale a importar el certificado personal en la lista MOK.

No se debe volver a ejecutar esta opción como medida de prueba sin revisar antes la configuración de claves: puede alterar la confianza configurada en el firmware.

***

### 3. ⚙️ Activación de Secure Boot en la Gigabyte X570 AORUS ELITE

Los nombres exactos y la ubicación de los menús pueden variar según la versión de BIOS. En esta instalación se utilizó la BIOS F40.

#### 3.1. 🔍 Preparación

Antes de modificar Secure Boot:

1. Comprobar que Debian arranca correctamente con el kernel personalizado y conservar un kernel alternativo funcional.
2. Entrar en la configuración UEFI de la placa base.
3. Localizar el apartado de **Boot / Secure Boot** y revisar el estado de las claves.
4. Si el firmware está en _Setup Mode_ o no tiene instaladas las claves de fábrica, revisar la opción de restauración de claves.

#### 3.2. 🔄 Restaurar las claves de fábrica

En el menú de Secure Boot, ejecutar **Restore Factory Keys** si es necesario para instalar las claves de confianza estándar.

Esta acción afecta a las claves del firmware. No firma el kernel personalizado, no firma módulos de Linux y no registra automáticamente el certificado MOK propio.

Si la BIOS solicita confirmación o un reinicio para aplicar los cambios, completar el procedimiento siguiendo las indicaciones que muestre la propia BIOS.

#### 3.3. ✅ Habilitar Secure Boot

Configurar Secure Boot como habilitado, siguiendo las opciones disponibles en la BIOS. Algunas configuraciones de firmware requieren desactivar el arranque heredado CSM o seleccionar un modo UEFI compatible antes de permitirlo.

Guardar los cambios y reiniciar.

#### 3.4. 🧪 Comprobar el estado desde Debian

```bash
mokutil --sb-state
```

Resultado observado:

```
SecureBoot enabled
```

Este resultado confirma que Secure Boot está habilitado desde el punto de vista del sistema operativo.

***

### 4. 🔑 Preparar el certificado MOK personalizado

#### 4.1. 🛡️ ¿Para qué sirve la MOK?

El certificado propio permite firmar el kernel personalizado y los módulos externos. Una vez inscrito mediante MOK Manager, el certificado queda disponible para el mecanismo de confianza de `shim` y para las comprobaciones de firma de módulos del kernel.

Los ficheros se encuentran en:

```bash
/home/$USER/.certs/secureboot/
```

| Fichero           | Función                                                                                                              |
| ----------------- | -------------------------------------------------------------------------------------------------------------------- |
| `MOK.priv`        | Clave privada utilizada para firmar. Debe mantenerse protegida.                                                      |
| `MOK.pem`         | Certificado X.509 en formato PEM.                                                                                    |
| `MOK.der`         | Certificado en formato DER, utilizado para la inscripción MOK.                                                       |
| `MOK-signing.pem` | Fichero combinado que contiene la clave privada y el certificado, utilizado en la configuración de firma del kernel. |

**Seguridad:** `MOK.priv` y `MOK-signing.pem` contienen material privado. No deben publicarse, subirse a repositorios ni compartirse con terceros. Los permisos de acceso deben limitarse al propietario.

#### 4.2. 🛠️ Crear el certificado (solo si se necesita regenerarlo)

El certificado existente ya está inscrito y funciona. No es necesario regenerarlo para repetir el proceso actual. Si se tuviera que crear uno nuevo, un ejemplo de generación sería:

```bash
mkdir -p "$HOME/.certs/secureboot"
chmod 700 "$HOME/.certs/secureboot"
cd "$HOME/.certs/secureboot"

openssl req -new -x509 -newkey rsa:2048 \
  -keyout MOK.priv \
  -out MOK.pem \
  -nodes \
  -days 3650 \
  -subj "/CN=Nombre del Certificado/"
```

Convertir el certificado a DER:

```bash
openssl x509 \
  -in MOK.pem \
  -outform DER \
  -out MOK.der
```

Crear el fichero combinado que puede utilizar el proceso de firma del kernel:

```bash
cat MOK.priv MOK.pem > MOK-signing.pem
chmod 600 MOK.priv MOK-signing.pem
chmod 644 MOK.pem MOK.der
```

Estos comandos son una referencia reproducible, no una afirmación de que se ejecutaran exactamente así al crear el certificado actualmente instalado.

#### 4.3. 📥 Inscribir la clave MOK

Solicitar la inscripción del certificado:

```bash
sudo mokutil --import /home/$USER/.certs/secureboot/MOK.der
```

La herramienta solicita definir una contraseña temporal para confirmar la inscripción en el siguiente arranque.

Después:

1. Reiniciar el equipo.
2. Entrar en **MOK Manager** cuando aparezca.
3. Seleccionar la opción para inscribir una clave, normalmente `Enroll MOK`.
4. Confirmar la inscripción del certificado.
5. Introducir la contraseña temporal configurada con `mokutil`.
6. Reiniciar y volver a Debian.

No se debe confundir esta contraseña temporal con la contraseña de la clave privada.

#### 4.4. ✅ Verificar la inscripción

```bash
sudo mokutil --test-key /home/$USER/.certs/secureboot/MOK.der
```

Resultado observado:

```bash
/home/$USER/.certs/secureboot/MOK.der is already in the built-in trusted keyring
```

Esto confirma que el certificado está presente en el conjunto de claves de confianza consultado por `mokutil`.

***

### 5. 🐧 Firmar la imagen del kernel personalizado

#### 5.1. ❓ Por qué fue necesario

El kernel personalizado [`6.19.14-ryzen9`](https://github.com/azagramac/linux-kernel) no disponía inicialmente de una firma EFI. \
La comprobación con `sbverify` mostraba que no había tabla de firmas.

Con Secure Boot habilitado, un kernel sin una firma aceptada puede ser rechazado durante el arranque. Por tanto, se firmó la imagen con la clave propia inscrita en MOK.

#### 5.2. ✍️ Firmar la imagen

Imagen del kernel:

```bash
/boot/vmlinuz-6.19.14-ryzen9
```

Comando utilizado para producir una imagen firmada:

```bash
sudo sbsign \
  --key /home/$USER/.certs/secureboot/MOK.priv \
  --cert /home/$USER/.certs/secureboot/MOK.pem \
  --output /boot/vmlinuz-6.19.14-ryzen9.signed \
  /boot/vmlinuz-6.19.14-ryzen9
```

El fichero de salida es una copia firmada de la imagen original.&#x20;

{% hint style="warning" %}
**No se debe utilizar el mismo fichero como entrada y salida.**
{% endhint %}

Tras verificar la firma, se sustituyó la imagen original por la imagen firmada y se eliminó el fichero intermedio `.signed`, de forma que no quedaran dos imágenes del mismo kernel en `/boot`.

Para una repetición futura, puede utilizarse un fichero temporal y sustituir la imagen original únicamente después de verificar el resultado. Antes de sobrescribir nada, conviene confirmar que el fichero de salida existe y que la firma es correcta.

#### 5.3. 🔎 Comprobar la firma EFI

```bash
sudo sbverify --list /boot/vmlinuz-6.19.14-ryzen9
```

El resultado mostró un firmante con el sujeto:

```bash
/CN=Nombre del Certificado/
```

Esta comprobación demuestra que la imagen contiene una firma EFI. Por sí sola, no demuestra que todos los componentes de la cadena de arranque acepten esa firma: también depende de la confianza configurada y del camino de arranque utilizado.

#### 5.4. 🔄 Actualizar GRUB

Después de sustituir la imagen:

```bash
sudo update-grub
```

El sistema detectó el kernel personalizado y el kernel alternativo de Debian.

La actualización de GRUB regenera su configuración para detectar los kernels disponibles. No firma el kernel ni sustituye el proceso de firma.

#### 5.5. 🧪 Verificar la firma del kernel en ejecución

Después de arrancar el kernel personalizado:

```bash
uname -r
```

Resultado esperado:

```bash
6.19.14-ryzen9
```

Consultar el firmante del módulo AMDGPU para ese kernel:

```bash
modinfo -k 6.19.14-ryzen9 -F signer amdgpu
```

Resultado observado:

```bash
Nombre del Certificado
```

Esta comprobación confirma que el módulo AMDGPU correspondiente al kernel indicado tiene esa firma. La verificación directa de la imagen EFI se realiza con `sbverify`.

***

### 6. 🧩 Firma de los módulos del kernel

#### 6.1. ❓ Por qué hay que firmar los módulos

Los módulos externos se cargan dentro del kernel y pueden ejecutar código con privilegios elevados. Con Secure Boot y las políticas de firma de módulos aplicables, un módulo sin una firma aceptada puede ser rechazado incluso cuando el kernel sí arranca correctamente.

La firma de la imagen del kernel no firma automáticamente los módulos externos. Cada módulo debe estar firmado con una clave de confianza.

En este sistema, los módulos de VirtualBox se compilaron para:

```bash
6.19.14-ryzen9
```

#### 6.2. 📦 Módulos de VirtualBox afectados (_pueden ser otros_)

Los módulos que se revisaron fueron:

| Módulo       | Función general                                        |
| ------------ | ------------------------------------------------------ |
| `vboxdrv`    | Controlador principal de VirtualBox.                   |
| `vboxnetflt` | Controlador de filtrado de red de VirtualBox.          |
| `vboxnetadp` | Controlador para adaptadores de red de tipo host-only. |

Ubicación observada:

```bash
/lib/modules/6.19.14-ryzen9/misc/
```

#### 6.3. 🔨 Recompilar los módulos antes de firmarlos

Cuando sea necesario reconstruir los módulos, utilizar el mecanismo de instalación de VirtualBox:

```bash
sudo /usr/lib/virtualbox/vboxdrv.sh setup
```

Este script prepara o recompila los módulos para el kernel instalado. No debe darse por hecho que los deja firmados con la MOK personal.

En la sesión documentada, después de ejecutar el script, los módulos se reconstruyeron, pero `modprobe` no pudo cargar `vboxdrv`. La causa se confirmó mediante el error de rechazo de clave.

#### 6.4. ✍️ Firmar los tres módulos

La herramienta `sign-file` proporcionada por los headers del kernel se encuentra en:

```bash
/usr/src/linux-headers-6.19.14-ryzen9/scripts/sign-file
```

Firmar los módulos con SHA-256, utilizando la clave privada y el certificado DER:

```bash
for m in vboxdrv vboxnetflt vboxnetadp; do
    sudo /usr/src/linux-headers-6.19.14-ryzen9/scripts/sign-file \
        sha256 \
        /home/$USER/.certs/secureboot/MOK.priv \
        /home/$USER/.certs/secureboot/MOK.der \
        "/lib/modules/6.19.14-ryzen9/misc/${m}.ko"
done
```

Los parámetros tienen este significado:

1. `sha256`: algoritmo de hash de la firma.
2. `MOK.priv`: clave privada utilizada para firmar.
3. `MOK.der`: certificado asociado a la clave privada.
4. `*.ko`: módulo que se va a firmar.

La firma se añade al módulo. Si un módulo se recompila posteriormente, su contenido cambia y debe volver a firmarse.

#### 6.5. 🔄 Actualizar la base de dependencias

Tras firmar los módulos:

```bash
sudo depmod -a 6.19.14-ryzen9
```

Esto actualiza la información de dependencias y módulos disponible para ese kernel. No firma los módulos; la firma ya se ha realizado con `sign-file`.

#### 6.6. 🔎 Verificar las firmas

Consultar el firmante de cada módulo:

```bash
for m in vboxdrv vboxnetflt vboxnetadp; do
    printf '%s: ' "$m"
    modinfo -k 6.19.14-ryzen9 -F signer "$m"
done
```

Resultado esperado en esta instalación:

```bash
vboxdrv: <Nombre del Certificado>
vboxnetflt: <Nombre del Certificado>
vboxnetadp: <Nombre del Certificado>
```

La información del módulo también se puede revisar individualmente:

```bash
modinfo -k 6.19.14-ryzen9 vboxdrv
```

El campo `signer` debe identificar el certificado utilizado.

#### 6.7. ▶️ Cargar los módulos

Cargar los tres módulos:

```bash
sudo modprobe vboxdrv
sudo modprobe vboxnetflt
sudo modprobe vboxnetadp
```

Si no aparece ningún error, comprobar que están cargados:

```bash
lsmod | grep '^vbox'
```

Resultado observado:

```bash
vboxnetadp 28672 0
vboxnetflt 36864 0
vboxdrv 634880 2 vboxnetadp,vboxnetflt
```

Esto confirma que los tres módulos estaban cargados en el kernel en ese momento.

***

### 7. 🧰 Troubleshooting: problemas encontrados y solución

#### 7.1. 🚫 El kernel no tiene tabla de firmas EFI

**Síntoma inicial:** `sbverify` indicó que no había tabla de firmas.

**Causa:** la imagen del kernel personalizado no estaba firmada mediante el mecanismo de firma EFI.

**Solución aplicada:** firmar `vmlinuz-6.19.14-ryzen9` con `sbsign`, comprobar el resultado con `sbverify --list` y actualizar GRUB.

Comandos de diagnóstico:

```bash
sudo sbverify --list /boot/vmlinuz-6.19.14-ryzen9
sudo update-grub
```

La firma de la imagen del kernel y la firma de módulos son operaciones distintas.

#### 7.2. 🔑 VirtualBox falla con _Key was rejected by service_

**Síntoma:**

```bash
modprobe: ERROR: could not insert 'vboxdrv': Key was rejected by service
```

**Interpretación:** el kernel rechazó la carga del módulo por un problema de aceptación de la firma o de confianza de la clave. En este caso, la investigación confirmó que los módulos de VirtualBox no tenían inicialmente una firma aceptada.

**Solución aplicada:**

1. Reconstruir los módulos mediante `vboxdrv.sh setup`.
2. Firmar `vboxdrv`, `vboxnetflt` y `vboxnetadp` con la MOK propia.
3. Ejecutar `depmod -a` para el kernel personalizado.
4. Reintentar la carga con `modprobe`.
5. Comprobar `signer` con `modinfo` y la carga efectiva con `lsmod`.

No es suficiente con que `modinfo` muestre un nombre de firmante: también debe comprobarse que el módulo se carga realmente.

#### 7.3. 🔧 El script de VirtualBox reconstruye módulos, pero siguen sin cargarse

El comando:

```bash
sudo /usr/lib/virtualbox/vboxdrv.sh setup
```

se utiliza para preparar o reconstruir los módulos. No garantiza que estos queden firmados con el certificado personal.

Si el problema persiste, comprobar:

```bash
uname -r
modinfo -F vermagic /lib/modules/$(uname -r)/misc/vboxdrv.ko
modinfo -F signer /lib/modules/$(uname -r)/misc/vboxdrv.ko
sudo modprobe vboxdrv
sudo journalctl -k -b --no-pager | grep -Ei 'vbox|verification|signature|key was rejected'
```

Interpretación:

* `uname -r` identifica el kernel en ejecución.
* `vermagic` permite comprobar si el módulo se compiló para un kernel compatible.
* `signer` muestra la identidad declarada del firmante.
* `modprobe` confirma si el kernel acepta cargar el módulo.
* El registro del kernel puede aportar el motivo del rechazo.

Si VirtualBox reconstruye los módulos de nuevo tras una actualización del kernel o de VirtualBox, es posible que haya que volver a firmarlos. La firma anterior no se debe dar por válida para un módulo recompilado.

#### 7.4. ⚠️ Errores de importación de certificados UEFI

En los registros de arranque aparecieron estos mensajes:

```bash
integrity: Problem loading X.509 certificate -22
integrity: Error adding keys to platform keyring UEFI:db
```

Se investigaron con:

```bash
sudo journalctl -k -b --no-pager |
  grep -B 2 -A 2 -E 'Problem loading X\.509 certificate|Error adding keys to platform keyring UEFI:db'
```

También se revisó el registro de integridad y Secure Boot:

```bash
sudo journalctl -k -b --no-pager |
  grep -Ei 'integrity:|secure boot|verification|key was rejected|Lockdown'
```

El registro mostró que se cargaron certificados de Microsoft y que también se cargaron los certificados de Debian, VirtualBox y `Nombre del Certificado` desde `UEFI:MokListRT`.

Extracto relevante:

```bash
integrity: Loaded X.509 cert 'Debian Secure Boot CA: ...'
integrity: Loaded X.509 cert 'VirtualBox: ...'
integrity: Loaded X.509 cert 'Nombre del Certificado: ...'
```

La comprobación de la MOK propia también fue satisfactoria:

```bash
sudo mokutil --test-key /home/$USER/.certs/secureboot/MOK.der
```

Resultado:

```bash
/home/$USER/.certs/secureboot/MOK.der is already in the built-in trusted keyring
```

**Conclusión documentada:** el certificado propio se encontraba inscrito y el kernel cargó el certificado MOK correspondiente. Los errores `-22` se produjeron al procesar dos entradas de `UEFI:db`, pero el mensaje no identifica sus nombres.

Aunque el orden del registro y el inventario previo de certificados permiten investigar cuáles podrían ser las entradas implicadas, no se ha demostrado de forma concluyente qué certificados originan esos dos errores. Tampoco debe atribuirse automáticamente el problema a la caducidad de un certificado sin una comprobación adicional.

No se modificaron las claves de la BIOS para intentar eliminar esos mensajes, porque el arranque firmado y la carga de los módulos propios ya funcionaban.

#### 7.5. 🔍 `dmesg` muestra errores

El comando:

```bash
sudo dmesg | grep -i error
```

```shellscript
sudo dmesg | grep -i fail
```

es útil como primer filtro, pero no constituye por sí solo un diagnóstico. Puede mostrar advertencias de inicialización que no impiden el funcionamiento normal del sistema.

Para investigar, conviene obtener el contexto del mensaje con `journalctl -k -b`, identificar el componente que lo emite y comprobar si hay un fallo funcional asociado.

En este caso, los mensajes de importación de algunas entradas de `UEFI:db` coexistían con la carga satisfactoria del certificado MOK propio y de los módulos de VirtualBox.

***

### 8. ✅ Comprobación final del sistema

Tras completar la configuración, las comprobaciones principales son las siguientes.

#### 8.1. 🛡️ Estado de Secure Boot

```bash
mokutil --sb-state
```

Resultado esperado:

```shellscript
SecureBoot enabled
```

#### 8.2. 🐧 Kernel en ejecución

```bash
uname -r
```

Resultado esperado:

```bash
6.19.14-ryzen9
```

#### 8.3. 🔏 Firma de la imagen EFI del kernel

```bash
sudo sbverify --list /boot/vmlinuz-6.19.14-ryzen9
```

Debe aparecer la firma del certificado `Nombre del Certificado`.

#### 8.4. 🧩 Firma de los módulos de VirtualBox

```bash
for m in vboxdrv vboxnetflt vboxnetadp; do
    printf '%s: ' "$m"
    modinfo -k 6.19.14-ryzen9 -F signer "$m"
done
```

Los tres módulos deben indicar `Nombre del Certificado`.

#### 8.5. 📋 Estado de los módulos

```bash
lsmod | grep '^vbox'
```

Deben aparecer los módulos que se hayan cargado correctamente.

#### 8.6. 📜 Verificar los registros del arranque actual

```bash
sudo journalctl -k -b --no-pager |
  grep -Ei 'secure boot|integrity:|verification|signature|key was rejected|vbox'
```

Revisar los mensajes según su contexto, sin asumir que todo mensaje que contiene la palabra `error` representa un fallo operativo.

***

### 9. 📋 Mantenimiento futuro

#### 🆙 Después de actualizar el kernel

1. Comprobar que la nueva imagen del kernel está firmada.
2. Si se compila un kernel personalizado nuevo, firmar su imagen EFI con el certificado propio antes de intentar arrancarlo con Secure Boot.
3. Comprobar que GRUB detecta el nuevo kernel.
4. Si los módulos externos deben recompilarse, reconstruirlos para la nueva versión del kernel y volver a firmarlos.
5. Comprobar las firmas y probar la carga de los módulos.

#### 📦 Después de actualizar VirtualBox

Si se reconstruyen sus módulos, verificar que siguen firmados. No asumir que el proceso de instalación conserva una firma anterior.

#### 🔒 Protección de las claves

La clave privada `MOK.priv` es el elemento más sensible de esta configuración. Quien tenga acceso a ella podría firmar código que se presente como firmado por el certificado propio. Debe mantenerse protegida y fuera de repositorios públicos.

El certificado público (`MOK.der` o `MOK.pem`) no tiene la misma sensibilidad que la clave privada, aunque debe conservarse una copia para poder documentar e identificar la clave inscrita.

***

### 10. 🏁 Estado final confirmado

A fecha de octubre de 2026, las verificaciones realizadas en el equipo dieron estos resultados:

* Secure Boot aparece habilitado.
* La MOK `Nombre del Certificado` está inscrita en el conjunto de confianza consultado por `mokutil`.
* La imagen del kernel `6.19.14-ryzen9` contiene una firma EFI del certificado propio.
* `modinfo` identifica a `Nombre del Certificado` como firmante de los módulos de VirtualBox.
* `depmod` se ejecutó para el kernel personalizado.
* `modprobe` cargó `vboxdrv`, `vboxnetflt` y `vboxnetadp` sin error.
* `lsmod` confirmó que los tres módulos estaban cargados.
* Persisten dos mensajes de importación de certificados de `UEFI:db` con error `-22`; su origen exacto no quedó identificado y no se deben atribuir a la MOK propia sin evidencia adicional.

**Resultado:** se consiguió utilizar el kernel personalizado y los módulos de VirtualBox con Secure Boot habilitado, manteniendo el kernel Debian alternativo disponible para recuperación.

<figure><img src="../.gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

