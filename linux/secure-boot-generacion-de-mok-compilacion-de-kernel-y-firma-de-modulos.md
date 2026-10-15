# Secure Boot: Generación de MOK, compilación de kernel y firma de módulos

<figure><img src="../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

#### 1. 🔐 Crear la clave privada y el certificado

En una instalación nueva, puedes generar la pareja con OpenSSL. Para máxima compatibilidad con UEFI, se suele utilizar RSA de 2048 bits y SHA-256. La documentación de DKMS de Debian también contempla certificados con uso extendido `codeSigning`, importante en kernels que validan ese atributo.

Instala las herramientas necesarias:

{% code title="" %}
```bash
sudo apt install -y openssl mokutil sbsigntool
```
{% endcode %}

Crea un directorio dedicado a la clave:

```bash
sudo -i
install -d -m 700 /root/secureboot
cd /root/secureboot
umask 077
```

Genera la clave privada y el certificado autofirmado:

```bash
openssl req \
    -new -x509 \
    -newkey rsa:2048 \
    -sha256 \
    -nodes \
    -keyout MOK.priv \
    -out MOK.pem \
    -days 36500 \
    -subj "/CN=Nombre del certificado/" \
    -addext "keyUsage=digitalSignature" \
    -addext "extendedKeyUsage=codeSigning"
```

{% hint style="info" %}
En `CN=Nombre del certificado`, podemos asignarle el nombre que deseemos a nuestro certificado
{% endhint %}

Convierte el certificado a DER, formato que requiere `mokutil --import`:

```bash
openssl x509 \
    -in MOK.pem \
    -outform DER \
    -out MOK.der
```

Generar `MOK-signing.pem`, combina la clave privada y el certificado público en un único archivo PEM que se utilizará explícitamente para firmar la imagen del kernel con `sbsign`:

```bash
cat MOK.priv MOK.pem > MOK-signing.pem
```

Este comando normaliza el certificado existente y guarda su contenido en `MOK-signing.pem`.

Ajusta los permisos:

```bash
sudo chmod 600 MOK.priv MOK-signing.pem
sudo chmod 644 MOK.pem MOK.der
```

{% hint style="warning" %}
Nota sobre `-nodes`: genera una clave privada sin contraseña, necesaria para que DKMS pueda firmar módulos automáticamente sin intervención. Por eso debe permanecer protegida y con permisos restrictivos.
{% endhint %}

#### 2. 🔍 Verificar la pareja generada

Comprueba que la clave privada corresponde al certificado:

```bash
openssl pkey -in MOK.priv -pubout -outform DER |
    sha256sum

openssl x509 -in MOK.pem -pubkey -noout |
    openssl pkey -pubin -outform DER |
    sha256sum
```

Los hashes deben coincidir.

Verificacion del MOK-signing.pem

{% code title="" %}
```bash
openssl pkey -in MOK-signing.pem -noout
openssl x509 -in MOK-signing.pem -noout -subject -issuer -dates
```
{% endcode %}

Comprueba también los atributos del certificado:

```bash
openssl x509 -in MOK.pem \
    -noout -subject -issuer -dates -text |
    grep -E 'Subject:|Issuer:|Not Before:|Not After :|Public-Key:|Key Usage|Extended Key Usage|Digital Signature|Code Signing'
```

Veredicto final

| **Archivo**       | **Uso**                                                                        |
| ----------------- | ------------------------------------------------------------------------------ |
| `MOK.priv`        | Clave privada para firmar.                                                     |
| `MOK.pem`         | Certificado público PEM para `sbsign`.                                         |
| `MOK.der`         | Certificado público DER para inscripción y `sign-file`.                        |
| `MOK-signing.pem` | Clave privada y certificado público concatenados para `CONFIG_MODULE_SIG_KEY`. |

#### 3. 🛡️ Inscribir la MOK en UEFI

<figure><img src="../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

La inscripción utiliza el certificado público, nunca la clave privada.

Solicita la inscripción:

```bash
sudo mokutil --import /root/secureboot/MOK.der
```

Introduce una contraseña temporal cuando la solicite `mokutil`. \
La contraseña temporal autoriza la inscripción pendiente durante el siguiente arranque. No es la contraseña de la clave privada. La inscripción debe confirmarse expresamente en MOK Manager.\
\
Después:

1. Reinicia el equipo.
2. Te saldra una pantalla en azul, MOK Manager.\
   ![](<../.gitbook/assets/image (225).png>)
3. Selecciona `Enroll MOK`.\
   ![](<../.gitbook/assets/image (223).png>)
4. Selecciona `Continue` y confirma con `Yes`.\
   ![](<../.gitbook/assets/image (224).png>)
5. Introduce la contraseña temporal.
6. Reinicia para volver a Debian.\
   ![](<../.gitbook/assets/image (226).png>)

Verifica la inscripción:

```bash
sudo mokutil --test-key /root/secureboot/MOK.der
```

✅ Resultado obtenido:

```bash
/root/secureboot/MOK.der is already in the built-in trusted keyring
```

#### 4. ⚙️ Configurar la firma automática en el `.config` del Kernel a compilar

En la configuración del kernel personalizado se habilitaron estas opciones:

```bash
CONFIG_MODULE_SIG=y
CONFIG_MODULE_SIG_ALL=y
CONFIG_MODULE_SIG_HASH="sha256"
CONFIG_MODULE_SIG_KEY="/root/secureboot/MOK-signing.pem"
CONFIG_SYSTEM_TRUSTED_KEYS=""
```

* `CONFIG_MODULE_SIG=y`: habilita el soporte de firmas de módulos.
* `CONFIG_MODULE_SIG_ALL=y`: hace que el proceso de compilación firme automáticamente los módulos que se construyen durante la compilación.
* `CONFIG_MODULE_SIG_HASH="sha256"`: establece SHA-256 como algoritmo de hash para las firmas.
* `CONFIG_MODULE_SIG_KEY`: apunta al archivo combinado `MOK-signing.pem`, que contiene la clave privada y el certificado. Kbuild lo utiliza para firmar los módulos durante la compilación cuando `CONFIG_MODULE_SIG_ALL=y`.
* `CONFIG_SYSTEM_TRUSTED_KEYS`: especifica certificados adicionales que se incorporan al anillo de confianza del kernel durante la compilación. No es la opción que activa la firma de módulos.

Importante: esta configuración firma automáticamente los módulos del kernel, pero no garantiza que la imagen EFI `vmlinuz` quede firmada con una firma reconocida por UEFI. Para ello se necesita el paso posterior con `sbsign`.

#### 5. 🏗️ Compilar y generar los paquetes Debian

El kernel se compila desde el repositorio personalizado, para una compilación optimizada para mi hardware uso:

{% code title="AMD Ryzen - Zen 3" %}
```bash
make -j$(nproc) bindeb-pkg \
  KCFLAGS="-march=znver3 -O2 -pipe" \
  KCPPFLAGS="-march=znver3 -mtune=znver3" \
  LOCALVERSION=-ryzen9 KDEB_PKGVERSION=6.19.14-ryzen9 \
  KDEB_CHANGELOG_DIST="trixie" \
  2>&1 | tee ../build-kernel.log
```
{% endcode %}

{% code title="Intel - Haswell" %}
```bash
make -j$(nproc) bindeb-pkg \
  KCFLAGS="-march=haswell -mtune=haswell -O2 -pipe" \
  KCPPFLAGS="-march=haswell -mtune=haswell" \
  LOCALVERSION=-intel KDEB_PKGVERSION=6.19.14-intel \
  KDEB_CHANGELOG_DIST="trixie" \
  2>&1 | tee ../build-kernel.log
```
{% endcode %}

Los parámetros relevantes son:

| **Parámetro**                     | **Descripción**                                                                                                  |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `-j$(nproc)`                      | Ejecuta compilaciones en paralelo utilizando el número de procesadores lógicos que devuelve `nproc`.             |
| `bindeb-pkg`                      | Genera paquetes binarios `.deb` del kernel para Debian.                                                          |
| `KCFLAGS`                         | Añade opciones al compilador C para los archivos del kernel que pasan por las reglas que utilizan esta variable. |
| `KCPPFLAGS`                       | Añade opciones al preprocesador C en las reglas de compilación correspondientes.                                 |
| `LOCALVERSION=-xxxxxxx`           | Añade `-ryzen9` o `-intel` a la versión de lanzamiento del kernel, normalmente visible en `uname -r`.            |
| `KDEB_PKGVERSION=6.19.14-xxxxxxx` | Establece la versión de los paquetes Debian generados.                                                           |
| `KDEB_CHANGELOG_DIST="trixie"`    | Indica la distribución Debian de destino en los metadatos de los paquetes.                                       |
| `\| tee ../build-kernel.log`      | Muestra la salida en pantalla y la guarda simultáneamente en el archivo de registro.                             |

#### 6. 📦 Instalar el kernel

Una vez haya terminado la compilación con exito, tendremos los binarios listos para instalar

Copiar los paquetes `.deb` al directorio temporal, de esta manera apt no dara un warning:

```bash
cp -rf *.deb /tmp/
cd /tmp
```

Instalar la imagen y las cabeceras del kernel:

```bash
sudo apt install -y \
  ./linux-image-6.19.14-ryzen9_6.19.14-xxxxxxx_amd64.deb \
  ./linux-headers-6.19.14-ryzen9_6.19.14-xxxxxxx_amd64.deb
```

Reinstalar `linux-libc-dev` (opcional):

```bash
sudo apt install --reinstall \
  ./linux-libc-dev_6.19.14-xxxxxxx_amd64.deb
```

#### 7. 🐧 Firmar la imagen EFI del kernel

Aunque los módulos se firmen durante la compilación, hay que firmar la imagen del kernel por separado:

```bash
sudo sbsign \
  --key /root/secureboot/MOK.priv \
  --cert /root/secureboot/MOK.pem \
  --output /boot/vmlinuz-6.19.14-xxxxxxx.signed \
  /boot/vmlinuz-6.19.14-xxxxxxx
```

#### 8. 🧪 Verificar y sustituir la imagen

Verificar la imagen recién firmada:

```bash
sudo sbverify --list /boot/vmlinuz-6.19.14-xxxxxxx.signed
```

Si la firma es correcta, sustituir la imagen original por la firmada, conservando una copia de seguridad si se desea disponer de una recuperación inmediata. En la instalación documentada, se sustituyó la imagen original y se eliminó el fichero intermedio `.signed` para no dejar dos imágenes del mismo kernel.\
\
📂 Renombramos la imagen original y la firmada

{% code title="" %}
```bash
sudo cp -a \
    /boot/vmlinuz-6.19.14-xxxxxxx \
    /boot/vmlinuz-6.19.14-xxxxxxx.backup

sudo cp \
    /boot/vmlinuz-6.19.14-xxxxxxx.signed \
    /boot/vmlinuz-6.19.14-xxxxxxx
```
{% endcode %}

#### 9. 🔄 Actualizar GRUB

```bash
sudo update-grub
```

{% hint style="info" %}
Este paso permite regenerar la configuración de arranque para detectar la imagen del kernel instalada. \
\
Ahora podemos reiniciar el sistema y arrancar con el nuevo kernel firmado.
{% endhint %}

**🔎** Verificar la firma de los módulos compilados despues del reinicio.

Antes de firmar manualmente los módulos externos, se puede comprobar la firma de un módulo incluido en el kernel, ejemplo `amdgpu`:

```bash
sudo modinfo -k 6.19.14-xxxxxxx -F signer amdgpu
```

Resultado observado:

```bash
Nombre del certificado asignado
```

Esto permite comprobar que el módulo AMDGPU del kernel personalizado identifica el certificado propio como firmante.

#### 10. 🧩 Firmar los módulos externos, en este ejemplo VirtualBox

La firma automática configurada para la compilación del kernel no garantiza que los módulos de VirtualBox, que se compilan por separado, queden firmados con la MOK propia.

Después de reconstruirlos, firmar individualmente los tres módulos:

```bash
for m in vboxdrv vboxnetflt vboxnetadp; do
    sudo /usr/src/linux-headers-6.19.14-xxxxxxx/scripts/sign-file \
        sha256 \
        /root/secureboot/MOK.priv \
        /root/secureboot/MOK.der \
        "/lib/modules/6.19.14-ryzen9/misc/${m}.ko"
done
```

🔗 Actualizar las dependencias:

```bash
sudo depmod -a 6.19.14-xxxxxxx
```

✅ Comprobar las firmas:

```bash
for m in vboxdrv vboxnetflt vboxnetadp; do
    printf '%s: ' "$m"
    modinfo -k 6.19.14-ryzen9 -F signer "$m"
done
```

🚀 Por último, cargar los módulos:

```bash
sudo modprobe vboxdrv
sudo modprobe vboxnetflt
sudo modprobe vboxnetadp
```

🩺 Comprobar los módulos cargados:

```bash
lsmod | grep '^vbox'
```

#### 11. 🛡️Comprobar la inscripción de la MOK

Verificar que el certificado está reconocido por el sistema de confianza de Secure Boot:

```bash
sudo mokutil --test-key \
    /root/secureboot/MOK.der
```

Resultado obtenido:

```bash
/root/secureboot/MOK.der is already in the built-in trusted keyring
```

#### 12. ⚙️ Modificar la configuración de DKMS

El fichero de configuración global de DKMS es:

```bash
/etc/dkms/framework.conf
```

Antes de modificarlo, crear una copia de seguridad:

```bash
sudo cp -a /etc/dkms/framework.conf \
    /etc/dkms/framework.conf.bak
```

Editar el fichero:

```bash
sudo nano /etc/dkms/framework.conf
```

Establecer las siguientes variables:

```bash
mok_signing_key="/root/secureboot/MOK.priv"
mok_certificate="/root/secureboot/MOK.der"
```

Estas variables indican a DKMS qué clave privada y certificado debe utilizar para firmar los módulos externos que gestione.

#### 📝 Observaciones

La firma del kernel, la firma de los módulos integrados en el kernel y la firma de los módulos externos gestionados por DKMS son operaciones independientes.

La configuración descrita centraliza la clave utilizada por DKMS, pero es necesario comprobar una compilación real para confirmar que la versión instalada aplica correctamente los parámetros. Los módulos existentes deben reconstruirse o firmarse de nuevo cuando corresponda.

La clave privada `MOK.priv` debe mantenerse protegida y no distribuirse junto con los paquetes del kernel.
