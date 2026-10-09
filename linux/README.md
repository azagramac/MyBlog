# Compilación del kernel con soporte de firma

<figure><img src="../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

⚙️ 1. Configurar la firma automática en `.config`

En la configuración del kernel personalizado se habilitaron estas opciones:

```bash
CONFIG_MODULE_SIG=y
CONFIG_MODULE_SIG_ALL=y
CONFIG_MODULE_SIG_HASH="sha256"
CONFIG_MODULE_SIG_KEY="/home/$USER/.certs/secureboot/MOK-signing.pem"
```

* `CONFIG_MODULE_SIG=y`: habilita el soporte de firmas de módulos.
* `CONFIG_MODULE_SIG_ALL=y`: hace que el proceso de compilación firme automáticamente los módulos que se construyen durante la compilación.
* `CONFIG_MODULE_SIG_HASH="sha256"`: establece SHA-256 como algoritmo de hash para las firmas.
* `CONFIG_MODULE_SIG_KEY=...`: especifica la clave privada y el certificado que se utilizarán para firmar los módulos.

Importante: esta configuración firma automáticamente los módulos del kernel, pero no garantiza que la imagen EFI `vmlinuz` quede firmada con una firma reconocida por UEFI. Para ello se necesita el paso posterior con `sbsign`.

#### 🏗️ 2. Compilar y generar los paquetes Debian

El kernel se compila desde el repositorio personalizado, para una compilación optimizada para mi hardware uso:

<pre class="language-bash"><code class="lang-bash">make -j$(nproc) bindeb-pkg KCFLAGS="-march=znver3" \
<strong>  LOCALVERSION=-ryzen9 \
</strong>  KDEB_PKGVERSION=6.19.14-ryzen9 \
  KDEB_CHANGELOG_DIST="trixie" \
  | tee ../build-kernel.log
</code></pre>

Los parámetros relevantes son:

* `-j$(nproc)`: paraleliza la compilación usando los procesadores lógicos disponibles.
* `KCFLAGS="-march=znver3"`: añade opciones de compilación específicas para Zen 3.
* `LOCALVERSION=-ryzen9`: identifica el kernel con el sufijo personalizado.
* `bindeb-pkg`: genera paquetes binarios Debian instalables.

Una vez instalados los paquetes generados, hay que comprobar que los módulos correspondientes están firmados y que el kernel instalado es el esperado.

#### 🔎 3. Verificar la firma de los módulos compilados

Antes de firmar manualmente los módulos externos, se puede comprobar la firma de un módulo incluido en el kernel:

```bash
modinfo -k 6.19.14-ryzen9 -F signer amdgpu
```

Resultado observado:

```bash
Nombre del certificado asignado
```

Esto permite comprobar que el módulo AMDGPU del kernel personalizado identifica el certificado propio como firmante.

### 🔏 Pasos posteriores a la compilación

#### 🐧 4. Firmar la imagen EFI del kernel

Aunque los módulos se firmen durante la compilación, hay que firmar la imagen del kernel por separado:

```bash
sudo sbsign \
  --key /home/$USER/.certs/secureboot/MOK.priv \
  --cert /home/$USER/.certs/secureboot/MOK.pem \
  --output /boot/vmlinuz-6.19.14-ryzen9.signed \
  /boot/vmlinuz-6.19.14-ryzen9
```

#### 🧪 5. Verificar y sustituir la imagen

Verificar la imagen recién firmada:

```bash
sudo sbverify --list /boot/vmlinuz-6.19.14-ryzen9.signed
```

Si la firma es correcta, sustituir la imagen original por la firmada, conservando una copia de seguridad si se desea disponer de una recuperación inmediata. En la instalación documentada, se sustituyó la imagen original y se eliminó el fichero intermedio `.signed` para no dejar dos imágenes del mismo kernel.

#### 🔄 6. Actualizar GRUB

```bash
sudo update-grub
```

Este paso permite regenerar la configuración de arranque para detectar la imagen del kernel instalada. No firma el kernel ni los módulos.

#### 🧩 7. Firmar los módulos externos de VirtualBox

La firma automática configurada para la compilación del kernel no garantiza que los módulos de VirtualBox, que se compilan por separado, queden firmados con la MOK propia.

Después de reconstruirlos, firmar individualmente los tres módulos:

```bash
for m in vboxdrv vboxnetflt vboxnetadp; do
    sudo /usr/src/linux-headers-6.19.14-ryzen9/scripts/sign-file \
        sha256 \
        /home/$USER/.certs/secureboot/MOK.priv \
        /home/$USER/.certs/secureboot/MOK.der \
        "/lib/modules/6.19.14-ryzen9/misc/${m}.ko"
done
```

Actualizar las dependencias:

```bash
sudo depmod -a 6.19.14-ryzen9
```

Comprobar las firmas:

```bash
for m in vboxdrv vboxnetflt vboxnetadp; do
    printf '%s: ' "$m"
    modinfo -k 6.19.14-ryzen9 -F signer "$m"
done
```

Por último, cargar los módulos:

```bash
sudo modprobe vboxdrv
sudo modprobe vboxnetflt
sudo modprobe vboxnetadp
```

Y comprobar que están cargados:

```bash
lsmod | grep '^vbox'
```

Resumen del flujo: compilación del kernel con firma automática de módulos → instalación del paquete → firma EFI de `vmlinuz` → verificación con `sbverify` → actualización de GRUB → recompilación y firma de módulos externos, si procede → `depmod` → carga y comprobación.
