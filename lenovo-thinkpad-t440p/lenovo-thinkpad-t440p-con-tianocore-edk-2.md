# Lenovo ThinkPad T440p con Tianocore EDK 2

<figure><img src="../.gitbook/assets/image (229).png" alt=""><figcaption></figcaption></figure>

Guía de compilación e instalación de Coreboot con el payload **TianoCore EDK II** para el Lenovo ThinkPad T440p, utilizando Debian y un programador SPI externo [CH341A](https://link.amazon/B08TKgW2F).

> ⚠️ **Advertencia:** modificar el firmware puede dejar el equipo inutilizable si se produce un error. Este procedimiento requiere experiencia con firmware, compilación y programación de chips SPI. Conserva una copia íntegra del firmware original antes de realizar cualquier modificación.

### 📋 Índice

1. [Actualizar el sistema](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-1-actualizar-el-sistema)
2. [Instalar las dependencias](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-2-instalar-las-dependencias)
3. [Preparar el directorio de trabajo](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-3-preparar-el-directorio-de-trabajo)
4. [Respaldar el firmware original](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-4-respaldar-el-firmware-original)
5. [Preparar el entorno de compilación](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-5-preparar-el-entorno-de-compilaci%C3%B3n)
6. [Compilar las herramientas de Coreboot](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-6-compilar-las-herramientas-de-coreboot)
7. [Extraer los componentes del firmware original](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-7-extraer-los-componentes-del-firmware-original)
8. [Obtener el archivo MRC](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-8-obtener-el-archivo-mrc)
9. [Configurar Coreboot y TianoCore](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-9-configurar-coreboot-y-tianocore)
10. [Compilar la ROM](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-10-compilar-la-rom)
11. [Comprobar y neutralizar Intel ME](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-11-comprobar-y-neutralizar-intel-me)
12. [Flashear mediante el CH341A](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-12-flashear-mediante-el-ch341a)
13. [Flashear desde Linux](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-13-flashear-desde-linux)
14. [Verificar el estado de Intel ME](https://chatgpt.com/c/6ad41e07-c350-83eb-a8da-e9977c022b8d#-14-verificar-el-estado-de-intel-me)

***

### 🔄 1. Actualizar el sistema

Actualiza los índices de los repositorios e instala las actualizaciones disponibles:

```bash
sudo apt update && sudo apt upgrade -y
```

### 📦 2. Instalar las dependencias

Instala las herramientas necesarias para compilar Coreboot, sus componentes auxiliares y las utilidades de firmware:

```bash
sudo apt install -y \
    build-essential \
    curl \
    flex \
    git \
    bison \
    gnat \
    libncurses5-dev \
    m4 \
    zlib1g-dev \
    flashrom \
    autoconf \
    automake \
    gettext \
    autopoint \
    pkg-config \
    grub-common \
    libfreetype6-dev \
    unifont \
    unifont-bin \
    xfonts-unifont \
    python-is-python3
```

> 💡 Las dependencias pueden variar según la versión de Debian y la revisión de Coreboot. Si la compilación solicita una biblioteca adicional, comprueba los requisitos de la revisión utilizada.

### 📁 3. Preparar el directorio de trabajo

Crea el directorio donde guardarás los respaldos y los archivos necesarios para construir la ROM:

```bash
mkdir -p ~/t440p-coreboot/
cd ~/t440p-coreboot/
```

Se utilizará esta estructura de trabajo:

```bash
~/t440p-coreboot/
├── 4mb_backup1.bin
├── 4mb_backup2.bin
├── 8mb_backup1.bin
├── 8mb_backup2.bin
├── t440p-original.rom
├── ifd.bin
├── me.bin
├── gbe.bin
├── mrc.bin
├── top.rom
├── bottom.rom
└── t440p-coreboot.rom
```

Los archivos se irán creando durante el procedimiento.

### 💾 4. Respaldar el firmware original

#### 🔌 4.1. Preparar el programador SPI

Necesitarás un programador compatible, como el CH341A, y las conexiones adecuadas para acceder a los chips SPI de la placa base.

Puedes consultar opciones de compra en [Amazon España](https://www.amazon.es/).

> ⚠️ Desconecta la alimentación del portátil y la batería principal antes de trabajar con los chips. Sigue las precauciones eléctricas y de conexión específicas de tu programador. No alimentes simultáneamente la placa desde fuentes incompatibles.

El T440p utiliza dos chips SPI en esta configuración:

* **Chip TOP:** 4 MiB.
* **Chip BOTTOM:** 8 MiB.

<figure><img src="../.gitbook/assets/image (230).png" alt=""><figcaption></figcaption></figure>

#### 🧩 4.2. Leer el chip TOP de 4 MiB

Realiza dos lecturas independientes:

```bash
sudo flashrom --programmer ch341a_spi -r 4mb_backup1.bin
sudo flashrom --programmer ch341a_spi -r 4mb_backup2.bin
```

Compara ambas copias:

```bash
diff 4mb_backup1.bin 4mb_backup2.bin
```

Si no aparece ninguna salida, las copias son idénticas.

#### 🧩 4.3. Leer el chip BOTTOM de 8 MiB

Realiza dos lecturas independientes:

```bash
sudo flashrom --programmer ch341a_spi -r 8mb_backup1.bin
sudo flashrom --programmer ch341a_spi -r 8mb_backup2.bin
```

Compara ambas copias:

```bash
diff 8mb_backup1.bin 8mb_backup2.bin
```

De nuevo, una salida vacía indica que ambas lecturas coinciden

#### 🛠️ 4.4. Seleccionar manualmente el chip SPI

En algunos casos, `flashrom` puede mostrar un mensaje indicando que existen varias definiciones para el mismo chip.

En el ejemplo original se utiliza:

```bash
-c W25Q64BV/W25Q64CV/W25Q64FV
```

Para el chip de 8 MiB, los comandos quedarían así:

```bash
sudo flashrom --programmer ch341a_spi \
    -r 8mb_backup1.bin \
    -c W25Q64BV/W25Q64CV/W25Q64FV

sudo flashrom --programmer ch341a_spi \
    -r 8mb_backup2.bin \
    -c W25Q64BV/W25Q64CV/W25Q64FV
```

**Importante:** selecciona la definición que corresponda realmente con la identificación del chip. No des por supuesto que esa familia es correcta para cualquier memoria SPI de 8 MiB.

Si las lecturas no coinciden:

1. Comprueba la conexión del programador y la pinza.
2. Verifica la identificación del chip.
3. Limpia los contactos con un producto adecuado para electrónica y deja que se evapore completamente.
4. Repite las lecturas hasta obtener dos copias idénticas.

No continúes mientras existan diferencias sin explicar.

#### 🗄️ 4.5. Crear la ROM original completa

Combina las dos regiones en el orden indicado:

```bash
cat 8mb_backup1.bin 4mb_backup1.bin > t440p-original.rom
```

Comprueba el tamaño:

```bash
stat -c '%n: %s bytes' t440p-original.rom
```

El resultado esperado es:

```bash
t440p-original.rom: 12582912 bytes
```

Verifica también los tamaños individuales:

```bash
stat -c '%n: %s bytes' \
    4mb_backup1.bin \
    8mb_backup1.bin
```

Calcula las sumas de comprobación:

```bash
sha256sum \
    4mb_backup1.bin \
    4mb_backup2.bin \
    8mb_backup1.bin \
    8mb_backup2.bin \
    t440p-original.rom
```

#### 🔐 4.6. Guardar copias de seguridad

**No continúes sin guardar el firmware original en varias ubicaciones.**

Conserva, como mínimo:

* Los dos respaldos originales de cada chip.
* La ROM completa combinada.
* Las sumas SHA-256.
* Una copia fuera del equipo, preferiblemente en otro dispositivo.

No modifiques ni sobrescribas estos archivos durante la compilación.

***

### 🏗️ 5. Preparar el entorno de compilación

#### 📥 5.1. Descargar Libreboot LBMK

Clona el repositorio de LBMK y utiliza su sistema de instalación de dependencias para Debian:

```bash
cd ~
git clone https://codeberg.org/libreboot/lbmk
cd lbmk/
sudo ./build dependencies debian
```

Este paso prepara las dependencias adicionales utilizadas por el entorno de construcción.

#### 📥 5.2. Descargar Coreboot

Clona el repositorio oficial de Coreboot:

```bash
cd ~
git clone https://review.coreboot.org/coreboot.git
cd coreboot/
```

Selecciona la revisión utilizada por esta guía:

```bash
git checkout d5e80fa9d22ae17ca80276e1d663333e0932c399
```

Inicializa y sincroniza los submódulos:

```bash
git submodule update --init --checkout
```

Comprueba la revisión seleccionada:

```bash
git rev-parse HEAD
```

> ⚠️ Esta guía utiliza una revisión concreta de Coreboot. No cambies de revisión sin verificar que siguen siendo compatibles las opciones de configuración, el soporte del T440p y el payload EDK II.

### ⌨️ 6. Compilar las herramientas de Coreboot

#### 🔀 6.1. Opcional: intercambiar Ctrl y Fn

Si quieres intercambiar el comportamiento de las teclas Ctrl y Fn, edita:

```bash
nano ~/coreboot/src/mainboard/lenovo/haswell/cmos.default
```

Busca la opción:

```bash
fn_ctrl_swap=Disable
```

Y cámbiala por:

```bash
fn_ctrl_swap=Enable
```

Guarda el archivo.

> 💡 Este cambio es opcional. No lo realices si prefieres mantener el comportamiento original del teclado.

#### 🧰 6.2. Compilar la cadena de herramientas

Desde el directorio de Coreboot:

```bash
cd ~/coreboot
make crossgcc-i386 CPUS=$(nproc)
```

Este comando compila las herramientas de desarrollo cruzado para la plataforma objetivo.

#### 🖥️ 6.3. Compilar Coreinfo

Genera la configuración predeterminada de Coreinfo:

```bash
make -C payloads/coreinfo olddefconfig
```

Compílalo:

```bash
make -C payloads/coreinfo
```

#### 🔧 6.4. Compilar ifdtool

```bash
cd ~/coreboot/util/ifdtool
make
```

La herramienta `ifdtool` permitirá extraer las regiones del firmware original.

***

### 🧩 7. Extraer los componentes del firmware original

Utiliza el archivo original de 12 MiB que creaste en el paso 4.

#### 📤 7.1. Extraer las regiones

Ejecuta:

```bash
cd ~/coreboot
./util/ifdtool/ifdtool -x ~/t440p-coreboot/t440p-original.rom
```

La herramienta generará archivos correspondientes a las regiones del firmware.

#### 📂 7.2. Copiar los componentes al directorio de trabajo

```bash
mv flashregion_0_flashdescriptor.bin \
    ~/t440p-coreboot/ifd.bin

mv flashregion_2_intel_me.bin \
    ~/t440p-coreboot/me.bin

mv flashregion_3_gbe.bin \
    ~/t440p-coreboot/gbe.bin
```

Estos archivos corresponden a:

| Archivo   | Función                                    |
| --------- | ------------------------------------------ |
| `ifd.bin` | Descriptor Intel Flash Descriptor          |
| `me.bin`  | Región de firmware Intel Management Engine |
| `gbe.bin` | Región de firmware de red Gigabit Ethernet |

Comprueba que existen:

```bash
ls -lh ~/t440p-coreboot/{ifd.bin,me.bin,gbe.bin}
```

No continúes si falta alguna región requerida.

***

### 🧠 8. Obtener el archivo MRC

Coreboot necesita el archivo `mrc.bin` para la inicialización de la memoria en esta configuración.

Hay dos métodos propuestos.

#### 📥 Método A: descargar MRC

El tutorial original proporciona esta dirección:

```bash
https://github.com/merge/skulls/blob/master/t440p/mrc.bin
```

**Advertencia:** las URL que contienen `/blob/` muestran normalmente una página HTML, no el archivo binario directamente. Para descargarlo, utiliza la URL `raw` correspondiente, si el archivo sigue disponible:

```bash
cd ~/t440p-coreboot
wget -O mrc.bin \
    https://raw.githubusercontent.com/merge/skulls/master/t440p/mrc.bin
```

Comprueba el archivo descargado:

```bash
file ~/t440p-coreboot/mrc.bin
```

No utilices un archivo HTML guardado accidentalmente con extensión `.bin`. Verifica también la procedencia y la compatibilidad del binario con el T440p.

#### 🧪 Método B: extraer MRC de un firmware compatible

Desde el directorio de Coreboot, compila `cbfstool`:

```bash
cd ~/coreboot
make -C util/cbfstool
```

A continuación, prepara las herramientas de ChromeOS:

```bash
cd ~/coreboot/util/chromeos
./crosfirmware.sh peppy
```

Extrae `mrc.bin` del firmware generado:

```bash
../cbfstool/cbfstool coreboot-*.bin \
    extract \
    -f mrc.bin \
    -n mrc.bin \
    -r RO_SECTION
```

Copia el archivo extraído:

```bash
mv mrc.bin ~/t440p-coreboot/mrc.bin
```

> ⚠️ El firmware `peppy` corresponde a otra plataforma. Antes de utilizar este método, confirma que el archivo obtenido contiene un MRC válido y compatible con el Haswell del T440p. No des por válida la compatibilidad por el mero hecho de que la extracción termine correctamente.

Comprueba el resultado:

```bash
file ~/t440p-coreboot/mrc.bin
ls -lh ~/t440p-coreboot/mrc.bin
```

***

### ⚙️ 9. Configurar Coreboot y TianoCore

#### 📝 9.1. Editar la configuración

Abre el archivo de configuración:

```bash
cd ~/coreboot
nano .config
```

Introduce las siguientes opciones, conservando cualquier otra configuración válida que requiera la revisión seleccionada.

```bash
CONFIG_MAINBOARD_FAMILY="ThinkPad T440p"
CONFIG_USE_OPTION_TABLE=y
CONFIG_VENDOR_LENOVO=y
CONFIG_CBFS_SIZE=0x400000

CONFIG_IFD_BIN_PATH="/home/$USER/t440p-coreboot/ifd.bin"
CONFIG_ME_BIN_PATH="/home/$USER/t440p-coreboot/me.bin"
CONFIG_GBE_BIN_PATH="/home/$USER/t440p-coreboot/gbe.bin"

CONFIG_HAVE_IFD_BIN=y
CONFIG_HAVE_MRC=y
CONFIG_MRC_FILE="/home/$USER/t440p-coreboot/mrc.bin"
CONFIG_VALIDATE_INTEL_DESCRIPTOR=y

CONFIG_H8_SUPPORT_BT_ON_WIFI=y
CONFIG_HAVE_ME_BIN=y
CONFIG_CHECK_ME=y
CONFIG_ME_REGION_ALLOW_CPU_READ_ACCESS=y
CONFIG_USE_ME_CLEANER=y
CONFIG_HAVE_GBE_BIN=y

CONFIG_DRIVERS_PS2_KEYBOARD=y

CONFIG_PAYLOAD_EDK2=y
CONFIG_EDK2_BOOT_MANAGER_ESCAPE=y
```

#### 📌 9.2. Ajustar las rutas

Sustituye `$USER` por el nombre real de tu usuario de Linux.

Por ejemplo, si tu usuario es `jose`, las rutas serán:

```bash
CONFIG_IFD_BIN_PATH="/home/$USER/t440p-coreboot/ifd.bin"
CONFIG_ME_BIN_PATH="/home/$USER/t440p-coreboot/me.bin"
CONFIG_GBE_BIN_PATH="/home/$USER/t440p-coreboot/gbe.bin"
CONFIG_MRC_FILE="/home/$USER/t440p-coreboot/mrc.bin"
```

No escribas literalmente `$USER` dentro de esas cadenas esperando que Coreboot lo expanda: la configuración no es un script de shell.

#### 🚪 9.3. Configurar TianoCore EDK II

Las siguientes opciones habilitan el payload EDK II y la tecla de acceso al administrador de arranque:

```bash
CONFIG_PAYLOAD_EDK2=y
CONFIG_EDK2_BOOT_MANAGER_ESCAPE=y
```

El objetivo es utilizar TianoCore como entorno de arranque UEFI en lugar de un payload alternativo.

#### 💾 9.4. Guardar la configuración

Genera el archivo de configuración predeterminada a partir de las opciones activas:

```bash
make savedefconfig
```

Consulta el resultado:

```bash
cat defconfig
```

Abre el configurador de Coreboot:

```bash
make nconfig
```

Desde `nconfig` puedes revisar y ajustar las opciones disponibles. Las teclas de función permiten guardar o cargar configuraciones; consulta la leyenda que muestra la versión concreta del configurador antes de utilizarlas.

> ⚠️ Comprueba que la revisión seleccionada admite todas las opciones del bloque anterior. Si una opción no existe o cambia de nombre, no la sustituyas por otra arbitrariamente: verifica su equivalente en esa revisión.

***

### 🏗️ 10. Compilar la ROM

Desde el directorio de Coreboot:

```bash
cd ~/coreboot
make
```

La compilación puede tardar varios minutos, dependiendo del procesador y de las herramientas instaladas.

#### ✅ 10.1. Comprobar el resultado

Si la compilación termina correctamente, debería aparecer un mensaje similar a:

```bash
Built lenovo/haswell (ThinkPad T440p)
```

Comprueba que se ha generado la imagen:

```bash
ls -lh ~/coreboot/build/coreboot.rom
```

#### 📦 10.2. Copiar la ROM al directorio de trabajo

```bash
cp ~/coreboot/build/coreboot.rom \
    ~/t440p-coreboot/t440p-coreboot.rom
```

Comprueba su tamaño:

```bash
stat -c '%n: %s bytes' \
    ~/t440p-coreboot/t440p-coreboot.rom
```

Antes de flashear, verifica que la imagen contiene las regiones esperadas y que su distribución es compatible con los dos chips SPI del equipo.

***

### 🛡️ 11. Comprobar y neutralizar Intel ME

Coreboot puede integrar `me_cleaner` para neutralizar determinadas funciones de [Intel Management Engine](https://www.intel.com/content/www/us/en/support/articles/000008927/software/chipset-software.html).

La opción incluida en la configuración es:

```bash
CONFIG_USE_ME_CLEANER=y
```

#### 🔍 11.1. Comprobar la imagen

Ejecuta:

```bash
cd ~/coreboot/util/me_cleaner
./me_cleaner.py -c \
    ~/t440p-coreboot/t440p-coreboot.rom
```

La opción `-c` comprueba la imagen y analiza la región Intel ME.

Una salida ilustrativa puede incluir:

```bash
Full image detected
The ME/TXE region goes from 0x3000 to 0x21000
Found FPT header at 0x3010
Found 1 partition(s)
Found FTPR header: FTPR partition spans from 0x1740 to 0xb1740
ME/TXE firmware version 9.1.45.3000
Public key match: Intel ME, firmware versions 9.0.x.x, 9.1.x.x
The AltMeDisable bit is SET
Checking the FTPR RSA signature... VALID
```

Las direcciones, las versiones y los resultados pueden variar.

#### 🔧 11.2. Neutralizar Intel ME

Según el procedimiento original:

```bash
./me_cleaner.py -S \
    ~/t440p-coreboot/t440p-coreboot.rom
```

* `-c`: comprueba la imagen.
* `-S`: aplica el procedimiento de neutralización correspondiente.

**Importante:** `-S` modifica la imagen. Comprueba la documentación de la versión de `me_cleaner` utilizada, así como la compatibilidad del resultado con Coreboot y el T440p. No confundas la neutralización parcial de ME con la eliminación física de su hardware.

Si no quieres utilizar esta integración, revisa la opción `CONFIG_USE_ME_CLEANER=y` antes de compilar. No basta con cambiar una opción después de haber generado la ROM.

Para otras modalidades de neutralización y reducción de la región Intel ME, consulta la documentación del proyecto [me\_cleaner](https://github.com/corna/me_cleaner).

***

### 🔌 12. Flashear mediante el CH341A

Este procedimiento escribe directamente los chips SPI. Es la vía de recuperación más útil cuando el firmware instalado no permite escribir la ROM desde Linux.

> 🚨 **No flashees hasta haber validado la ROM, comprobado los tamaños y conservado varias copias del firmware original.**

#### ✂️ 12.1. Separar la ROM en dos archivos

El ejemplo presupone que la imagen completa está organizada con la región de 8 MiB primero y la de 4 MiB después.

```bash
cd ~/t440p-coreboot
```

Extrae los primeros 8 MiB:

```bash
dd if=t440p-coreboot.rom \
    of=bottom.rom \
    bs=1M \
    count=8 \
    status=progress
```

Extrae los 4 MiB restantes:

```bash
dd if=t440p-coreboot.rom \
    of=top.rom \
    bs=1M \
    skip=8 \
    status=progress
```

Verifica los tamaños:

```bash
stat -c '%n: %s bytes' bottom.rom top.rom
```

Los resultados esperados son:

| Archivo      | Tamaño          |
| ------------ | --------------- |
| `bottom.rom` | 8.388.608 bytes |
| `top.rom`    | 4.194.304 bytes |

> ⚠️ La división por tamaño no garantiza por sí sola que el contenido corresponda al chip correcto. Confirma el orden de las regiones y la disposición de la imagen antes de escribir. Un orden incorrecto puede inutilizar el equipo.

#### 🔥 12.2. Escribir el chip TOP de 4 MiB

```bash
sudo flashrom --programmer ch341a_spi -w top.rom
```

#### 🔥 12.3. Escribir el chip BOTTOM de 8 MiB

```bash
sudo flashrom --programmer ch341a_spi -w bottom.rom
```

Si `flashrom` solicita seleccionar una definición específica del chip, verifica primero su identificación. Solo si corresponde a la familia indicada en el apartado 4, el comando sería:

```bash
sudo flashrom --programmer ch341a_spi \
    -w bottom.rom \
    -c W25Q64BV/W25Q64CV/W25Q64FV
```

No desconectes el programador mientras se esté escribiendo o verificando la memoria.

Una vez finalizada la escritura, utiliza la opción de verificación de `flashrom` o vuelve a leer los chips y compara el contenido con las imágenes escritas. No des por terminado el proceso si aparecen errores de verificación.

***

### 🐧 13. Flashear desde Linux

> ⚠️ Este método puede no funcionar en una instalación original de fábrica. La escritura interna depende de las protecciones del firmware, del descriptor Intel y de la configuración del sistema. El programador externo es preferible si necesitas una vía de recuperación independiente.

Retira el módulo `lpc_ich` si está cargado:

```bash
sudo modprobe -r lpc_ich
```

Intenta escribir la imagen completa:

```bash
sudo flashrom -p internal \
    -w ~/t440p-coreboot/t440p-coreboot.rom
```

El nombre del archivo debe coincidir exactamente con el generado en el paso 10: `t440p-coreboot.rom`.

Si la operación falla, no fuerces la escritura ni elimines protecciones sin identificar primero la causa. Comprueba los mensajes de `flashrom` y las restricciones de escritura de la plataforma.

***

### 🔬 14. Verificar el estado de Intel ME

Después del flasheo, puedes examinar el estado de Intel Management Engine mediante `intelmetool`.

#### 🧰 14.1. Compilar intelmetool

```bash
cd ~/coreboot/util/intelmetool
make
```

#### 🔍 14.2. Consultar el estado

```bash
sudo ./intelmetool -m
```

Una salida ilustrativa del procedimiento original es:

```bash
Checking for development libraries: pci and zlib... found.
Bad news, you have a `QM87 Express LPC Controller` so you have ME hardware on board and you can't control or disable it, continuing...
MEI found: [8086:8c3a] 8 Series/C220 Series Chipset Family MEI Controller #1
ME Status : 0x1e020191
ME Status 2 : 0x164d2142
ME: FW Partition Table : OK
ME: Bringup Loader Failure : NO
ME: Firmware Init Complete : NO
ME: Manufacturing Mode : YES
ME: Boot Options Present : NO
ME: Update In Progress : NO
ME: Current Working State : Initializing
ME: Current Operation State : Bring up
ME: Current Operation Mode : Debug
ME: Error Code : No Error
ME: Progress Phase : BUP Phase
ME: Power Management Event : Pseudo-global reset
ME: Progress Phase State : 0x4d
ME: Extend SHA-256: 9bcd2f1eb9b163690f6ee10a57fb83f9c55447eaa29c91040ec4c77f6bc7fa3c
ME: failed to become ready
ME: failed to become ready
ME: GET FW VERSION message failed
```

Esta salida es un ejemplo, no un resultado garantizado.

Si has utilizado `me_cleaner`, la imposibilidad de inicializar ME puede ser coherente con la neutralización aplicada. Sin embargo, **no interpretes una salida concreta como prueba suficiente de que todo el firmware está correctamente configurado**.

Comprueba también que:

* El portátil completa el POST.
* TianoCore EDK II se inicia correctamente.
* El administrador de arranque detecta los dispositivos previstos.
* El teclado y los dispositivos integrados funcionan.
* La configuración de la memoria y el resto del hardware es estable.

***

### ✅ Lista de comprobación final

Antes de dar el proceso por terminado, verifica estos puntos:

* 💾 Las dos lecturas de cada chip original son idénticas.
* 🔐 El firmware original está guardado en varias ubicaciones.
* 🧩 Las regiones IFD, ME y GbE se han extraído correctamente.
* 🧠 El archivo MRC es válido y compatible con el T440p.
* ⚙️ Las rutas de `.config` apuntan a los archivos correctos.
* 🏗️ Coreboot se compila sin errores.
* 📦 La ROM generada tiene la estructura y el tamaño esperados.
* 🔍 Se ha comprobado el resultado de `me_cleaner`, si se ha utilizado.
* 🔌 Los archivos TOP y BOTTOM corresponden a los chips correctos.
* 🛠️ La escritura y la verificación de los chips han finalizado sin errores.
* 🚀 El portátil inicia Coreboot y TianoCore EDK II.

{% hint style="info" %}
Conserva los respaldos originales la BIOS incluso después de comprobar que el equipo funciona correctame
{% endhint %}

Nota técnica: antes de ejecutar esta guía literalmente, conviene validar la compatibilidad de la revisión concreta de Coreboot con EDK II, MRC y las opciones de Intel ME. El tutorial de partida contiene algunos pasos que dependen de versiones específicas y no deben considerarse universalmente válidos para cualquier T440p.
