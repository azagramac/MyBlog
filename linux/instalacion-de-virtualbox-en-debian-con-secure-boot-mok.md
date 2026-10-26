# Instalación de VirtualBox en Debian con Secure Boot (MOK)

Esta guía detalla el proceso completo para configurar, compilar, firmar y mantener **VirtualBox** de forma automática en **Debian Trixie** utilizando un kernel firmado con **MOK (Machine Owner Key)**, **Secure Boot** habilitado y **DKMS 3.x**.

***

### 🛠️ 1. Infraestructura Automática de DKMS

El paquete oficial `.deb` de VirtualBox no incluye un archivo de configuración estándar para DKMS, lo que impide que delegue el firmado de manera nativa. Para solucionarlo de raíz, estructuramos un archivo personalizado.

#### Paso 1: Configurar el Framework Global

Asegúrate de que las siguientes variables estén correctamente declaradas y sin comentar en tu archivo `/etc/dkms/framework.conf`:

```ini
mok_signing_key=/root/secureboot/MOK.priv
mok_certificate=/root/secureboot/MOK.der
```

#### Paso 2: Crear el archivo `dkms.conf` para VirtualBox

Para que DKMS 3.x gestione la compilación multipaquete mediante arrays indexados, inyectamos la configuración directamente en las fuentes de VirtualBox:

```bash
cat << 'EOF' > /usr/src/vboxhost-7.2.22/dkms.conf
PACKAGE_NAME="virtualbox"
PACKAGE_VERSION="7.2.22"

# Comando de compilación unificado para los tres módulos
MAKE="make -C vboxdrv KERNELRELEASE=${kernelver} && make -C vboxnetflt KERNELRELEASE=${kernelver} && make -C vboxnetadp KERNELRELEASE=${kernelver}"
CLEAN="make -C vboxdrv clean && make -C vboxnetflt clean && make -C vboxnetadp clean"

# Módulo 0: vboxdrv
BUILT_MODULE_NAME[0]="vboxdrv"
BUILT_MODULE_LOCATION[0]="vboxdrv"
DEST_MODULE_LOCATION[0]="/kernel/drivers/misc"

# Módulo 1: vboxnetflt
BUILT_MODULE_NAME[1]="vboxnetflt"
BUILT_MODULE_LOCATION[1]="vboxnetflt"
DEST_MODULE_LOCATION[1]="/kernel/drivers/misc"

# Módulo 2: vboxnetadp
BUILT_MODULE_NAME[2]="vboxnetadp"
BUILT_MODULE_LOCATION[2]="vboxnetadp"
DEST_MODULE_LOCATION[2]="/kernel/drivers/misc"

AUTOINSTALL="yes"
EOF
```

***

### 🚀 2. Ciclo de Compilación y Firma Automática

Con la estructura lista, registramos el módulo en el árbol de DKMS para disparar el firmado digital automático con tu clave de Secure Boot (`Jose l. Azagra`).

```bash
# Registrar el módulo en DKMS
dkms add -m virtualbox -v 7.2.22

# Compilar, firmar e instalar forzando la sobrescritura de binarios antiguos
dkms install -m virtualbox -v 7.2.22 --force
```

#### 🔍 Verificación No Destructiva de la Firma

Puedes comprobar que el kernel aceptará los controladores validando que tu firma digital se ha incrustado correctamente:

```bash
modinfo vboxdrv | grep -E "signer|crypto"
```

Si todo es correcto, levanta los servicios del sistema:

```bash
modprobe vboxdrv
systemctl restart virtualbox
```

***

### 👥 3. Gestión de Permisos e Inclusión de Hardware (USB)

Para poder operar VirtualBox y mapear dispositivos USB reales desde tu sesión de usuario sin privilegios de `root`, aplica las siguientes políticas de grupo:

```bash
# Añadir tu usuario al grupo de gestión de VirtualBox
sudo usermod -aG vboxusers tu_usuario

# Añadir tu usuario al grupo de acceso a hardware/USB en Debian
sudo usermod -aG lp tu_usuario
```

_> ⚠️ **Nota Crítica:** Es obligatorio cerrar sesión o reiniciar el sistema (`sudo reboot`) para que los nuevos privilegios se apliquen a tu entorno gráfico._

#### 🔌 Activación de USB 2.0 / 3.0

Descarga e instala el paquete de extensiones oficial coincidente con tu versión de VirtualBox para habilitar las controladoras de alta velocidad:

```bash
wget https://download.virtualbox.org/virtualbox/7.2.22/Oracle_VirtualBox_Extension_Pack-7.2.22.vbox-extpack
sudo vboxmanage extpack install Oracle_VirtualBox_Extension_Pack-7.2.22.vbox-extpack
```

***

### 🔄 4. Escenario de Actualizaciones Futuras

El comportamiento del sistema ante cambios diferirá según el componente que se actualice:

#### Caso A: Actualización del Kernel de Debian (100% Automático)

Cuando el sistema reciba parches o nuevas versiones del Kernel de Linux, **DKMS se encargará de todo de forma invisible**. Compilará los drivers y los firmará con tu MOK en segundo plano. No requiere intervención, cuando termine reiniciar el sistema y comprobar.&#x20;

#### Caso B: Actualización de la versión de VirtualBox (Requiere Mantenimiento)

Al instalar un nuevo `.deb` de VirtualBox (ej. de la versión `7.2.22` a la `7.2.24`), el instalador reemplazará el directorio de las fuentes eliminando nuestro archivo `dkms.conf`. El servicio fallará momentáneamente.

**🛠️ Protocolo de restauración en 1 minuto:**

Cuando se actualice VirtualBox, ejecuta el bloque adaptando el número de la nueva versión (ejemplo con `7.2.24`):

```bash
# 1. Crear el archivo dkms.conf en la nueva carpeta de fuentes
sudo cat << 'EOF' > /usr/src/vboxhost-xxxxxxxx/dkms.conf
PACKAGE_NAME="virtualbox"
PACKAGE_VERSION="xxxxxxxx" # Cambialo por la versio nueva
MAKE="make -C vboxdrv KERNELRELEASE=${kernelver} && make -C vboxnetflt KERNELRELEASE=${kernelver} && make -C vboxnetadp KERNELRELEASE=${kernelver}"
CLEAN="make -C vboxdrv clean && make -C vboxnetflt clean && make -C vboxnetadp clean"
BUILT_MODULE_NAME[0]="vboxdrv"
BUILT_MODULE_LOCATION[0]="vboxdrv"
DEST_MODULE_LOCATION[0]="/kernel/drivers/misc"
BUILT_MODULE_NAME[1]="vboxnetflt"
BUILT_MODULE_LOCATION[1]="vboxnetflt"
DEST_MODULE_LOCATION[1]="/kernel/drivers/misc"
BUILT_MODULE_NAME[2]="vboxnetadp"
BUILT_MODULE_LOCATION[2]="vboxnetadp"
DEST_MODULE_LOCATION[2]="/kernel/drivers/misc"
AUTOINSTALL="yes"
EOF

# 2. Registrar y forzar la instalación con firmado MOK automático
sudo dkms add -m virtualbox -v xxxxxxxx
sudo dkms install -m virtualbox -v xxxxxxxx --force

# 3. Reiniciar servicios y descargar el nuevo Extension Pack coincidente
sudo systemctl restart virtualbox
```
