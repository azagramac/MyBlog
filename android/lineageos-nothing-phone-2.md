---
description: >-
  Objetivo: Instalar LineageOS 23.2 con KernelSU en modo LKM, firmar las
  particiones mediante AVBRoot, cerrar el bootloader y mantener la integridad de
  arranque (AVB / dm-verity).
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
  anchors:
    visible: true
---

# 📱 LineageOS - Nothing Phone 2

## 📱 Nothing Phone 2  - LineageOS 23.2 + KernelSU LKM + AVBRoot



**Dispositivo:** Nothing Phone 2 (Pong)\
**ROM:** LineageOS 23.2\
**Root:** KernelSU LKM\
**Gestión de claves y firmas:** AVBRoot\
**Estado final del bootloader:** Bloqueado (Locked)\
**Estado AVB:** Yellow (clave personalizada)

***

## 🖥️ A. Preparación en el PC (Linux Debian 13)

### 🔐 A.1. Generación de claves AVB y OTA

{% hint style="info" %}
Este bloque ya está ejecutado y verificado. Se conserva como referencia por si en el futuro necesitas reconstruir el entorno desde cero.

**IMPORTANTE:** Realiza una copia de seguridad cifrada de las claves generadas. Son necesarias para firmar las futuras actualizaciones.
{% endhint %}

Crear el directorio de claves:

```bash
mkdir -p keys-lineageos
```

**Generar la clave privada AVB:**

```bash
./avbroot key generate-key -o keys-lineageos/avb.key
```

**Generar la clave pública AVB en formato PKMD:**

```bash
./avbroot key encode-avb \
  -k keys-lineageos/avb.key \
  -o keys-lineageos/avb_pkmd.bin
```

**Generar la clave privada OTA:**

```bash
./avbroot key generate-key -o keys-lineageos/ota.key
```

**Generar el certificado OTA:**

```bash
./avbroot key generate-cert \
  -k keys-lineageos/ota.key \
  -o keys-lineageos/ota.crt \
  --subject "CN=OTA"
```

**Descargas:**

* KernelSU: [https://github.com/tiann/KernelSU/releases](https://github.com/tiann/KernelSU/releases)
  * APK de gestión.
  * Módulo LKM `.ko` para ARM64.
  * Ejecutable `ksud` para Linux x86\_64.
* PlayIntegrityFork: [https://github.com/osm0sis/PlayIntegrityFork/releases](https://github.com/osm0sis/PlayIntegrityFork/releases)
* ZygiskNext: [https://github.com/Dr-TSNG/ZygiskNext/releases](https://github.com/Dr-TSNG/ZygiskNext/releases)
* ROM LineageOS: [https://download.lineageos.org/devices/Pong/builds](https://download.lineageos.org/devices/Pong/builds)
* Gapps: [https://github.com/MindTheGapps/16.0.0-arm64/releases](https://github.com/MindTheGapps/16.0.0-arm64/releases)

### 🧩 A.2. Extracción de boot.img y parcheo con KernelSU LKM

Extraer la partición `boot` de la ROM oficial:

```bash
./avbroot ota extract \
  --input rom-lineageos/lineage-23.2-20261111-nightly-Pong-signed.zip \
  --directory /tmp/orig-boot \
  --partition boot
```

Dar permisos de ejecución a `ksud`:

```bash
chmod +x root/ksud-x86_64-unknown-linux-musl
```

Parchear `boot.img` con el módulo KernelSU LKM:

```bash
./root/ksud-x86_64-unknown-linux-musl boot-patch \
  -b /tmp/orig-boot/boot.img \
  -m root/lkm-aarch64-android12-5.10_kernelsu.ko \
  -o root/
```

Eliminar los archivos temporales:

```bash
rm -rf /tmp/orig-boot
```

### ✍️ A.3. Firma de la OTA con AVBRoot

Generar una nueva OTA integrando el `boot.img` parcheado y firmando las particiones con nuestras claves:

```bash
./avbroot ota patch \
  --input rom-lineageos/lineage-23.2-20261111-nightly-Pong-signed.zip \
  --output rom-lineageos/lineage-23.2-20261111-nightly-Pong-avbroot-ksu.zip \
  --key-avb keys-lineageos/avb.key \
  --key-ota keys-lineageos/ota.key \
  --cert-ota keys-lineageos/ota.crt \
  --prepatched root/kernelsu_patched_*.img \
  --clear-vbmeta-flags
```

El argumento:

```bash
--clear-vbmeta-flags
```

se utiliza cuando la imagen `vbmeta` presenta flags que deshabilitan AVB y AVBRoot informa de que no puede continuar con la verificación.

Si aparece un error similar a:

```bash
Verified boot is disabled by vbmeta's header flags
```

y se ha confirmado que es necesario habilitar la verificación, repetir el proceso añadiendo:

```bash
--clear-vbmeta-flags
```

{% hint style="info" %}
[No utilizar este argumento indiscriminadamente en todas las compilaciones.](#user-content-fn-1)[^1]
{% endhint %}

### 🔎 A.4. Verificación de firmas y extracción de imágenes

Verificar la integridad y las firmas de la OTA generada:

```bash
./avbroot ota verify \
  --input rom-lineageos/lineage-23.2-20261111-nightly-Pong-avbroot-ksu.zip \
  --public-key-avb keys-lineageos/avb_pkmd.bin \
  --cert-ota keys-lineageos/ota.crt
```

Extraer todas las particiones firmadas para su posterior instalación mediante Fastboot:

```bash
./avbroot ota extract \
  --input rom-lineageos/lineage-23.2-20261111-nightly-Pong-avbroot-ksu.zip \
  --directory rom-lineageos/signed-images/ \
  --all
```

***

## 📲 B. Preparación del Nothing Phone 2

### ⚙️ B.1. Requisitos previos en Nothing OS

Antes de comenzar, comprobar los siguientes puntos:

1. **Versión del sistema:** En `Ajustes → Acerca del teléfono`, verificar que el dispositivo ejecuta Android 16 (Nothing OS 4.1).
2. **Comprobación de hardware:** Probar llamadas, datos móviles y conexión Wi-Fi.
3. **Bloqueo de pantalla:** Desactivar temporalmente PIN, contraseña y huella.
4. **Aplicaciones bancarias:** Desvincular los tokens de autenticación o cerrar sesión
5. **Cuentas:** Eliminar las cuentas de Google del dispositivo.
6. **Opciones de desarrollador:** Activar:
   * Desbloqueo OEM.
   * Depuración USB.

***

## 🔓 C. Desbloqueo del bootloader

### C.1. Reiniciar al bootloader

Conectar el teléfono al PC y ejecutar:

```bash
adb -d reboot bootloader
```

Comprobar que Fastboot detecta el dispositivo:

```bash
fastboot devices
```

### C.2. Desbloquear el bootloader

{% hint style="warning" %}
Este procedimiento realiza un restablecimiento de fábrica y elimina todos los datos del teléfono.
{% endhint %}

Ejecutar:

```bash
fastboot flashing unlock
```

Confirmar el desbloqueo en la pantalla del teléfono utilizando los botones de volumen y encendido.

{% hint style="info" %}
Si después del formateo el teléfono arranca directamente en Nothing OS, apagarlo y volver a encenderlo manteniendo pulsados **Volumen Abajo + Encendido** para acceder al bootloader.
{% endhint %}

***

## 💾 D. Flasheo de las particiones iniciales

### D.1. Instalar las imágenes firmadas mediante AVBRoot

Desde el PC, ejecutar los siguientes comandos:

```bash
fastboot flash boot rom-lineageos/signed-images/boot.img
fastboot flash dtbo rom-lineageos/signed-images/dtbo.img
fastboot flash vbmeta rom-lineageos/signed-images/vbmeta.img
fastboot flash vendor_boot rom-lineageos/signed-images/vendor_boot.img
```

Reiniciar al bootloader:

```bash
fastboot reboot bootloader
```

Instalar las particiones restantes:

```bash
fastboot flash recovery rom-lineageos/signed-images/recovery.img
fastboot flash vbmeta_system rom-lineageos/signed-images/vbmeta_system.img
fastboot flash vbmeta_vendor rom-lineageos/signed-images/vbmeta_vendor.img
```

### D.2. Entrar en Recovery y formatear los datos

Reiniciar directamente en Recovery:

```bash
fastboot reboot recovery
```

En el teléfono, seleccionar:

**Factory reset → Format data / factory reset**

Confirmar el borrado completo de los datos.

***

## 📦 E. Instalación de LineageOS en ambas ranuras

### ⚠️ E.1. Consideraciones importantes

{% hint style="danger" %}
**No instalar GApps desde Recovery.**

Las GApps se instalarán posteriormente mediante KernelSU como módulos Systemless. Instalar GApps desde Recovery podría alterar las particiones y romper dm-verity al volver a bloquear el bootloader.
{% endhint %}

### E.2. Primera instalación (ranura A)

En el teléfono:

`Apply update → Apply from ADB`

En el PC:

```bash
adb -d sideload rom-lineageos/lineage-23.2-20261111-nightly-Pong-avbroot-ksu.zip
```

Esperar a que finalice el proceso.

### E.3. Segunda instalación (ranura B)

En el teléfono:

`Advanced → Reboot to recovery`

Una vez de vuelta en Recovery:

`Apply update → Apply from ADB`

Ejecutar nuevamente en el PC:

```bash
adb -d sideload rom-lineageos/lineage-23.2-20261111-nightly-Pong-avbroot-ksu.zip
```

De esta manera, la ROM firmada queda instalada en ambas ranuras.

***

## 🔒 F. Primer arranque y bloqueo del bootloader

### F.1. Primer arranque de prueba

En Recovery, seleccionar:

`Reboot system now`

Completar el inicio rápido de LineageOS sin realizar todavía la configuración inicial.

{% hint style="info" %}
Esta configuración provisional se eliminará durante el siguiente restablecimiento de fábrica, que se produce al bloquear el bootloader.
{% endhint %}

Activar nuevamente la depuración USB y reiniciar al bootloader:

```bash
adb -d reboot bootloader
```

### F.2. Instalar la clave pública AVB personalizada

Flashear la clave pública generada anteriormente:

```bash
fastboot flash avb_custom_key keys-lineageos/avb_pkmd.bin
```

### F.3. Bloquear el bootloader

Ejecutar:

```bash
fastboot flashing lock
```

Confirmar el bloqueo en la pantalla del teléfono utilizando los botones físicos.

{% hint style="warning" %}
El bloqueo del bootloader provoca un nuevo restablecimiento de fábrica. \
Se eliminarán todos los datos del dispositivo.
{% endhint %}

### F.4. Comprobación del estado AVB

Al arrancar, el teléfono mostrará el aviso amarillo correspondiente al uso de una clave AVB personalizada.

**Estado esperado:**

* **Bootloader:** Locked.
* **AVB:** Yellow.
* **Clave de verificación:** Personalizada.
* **dm-verity:** Activo.

{% hint style="warning" %}
**Nunca desactivar el Desbloqueo OEM desde Android después de haber bloqueado el bootloader.**
{% endhint %}

***

## 🛠️ G. Configuración final: KernelSU, GApps y Play Integrity

### G.1. Configuración inicial de LineageOS

Completar el asistente inicial de LineageOS y activar la depuración USB.

### G.2. Instalar la aplicación gestora de KernelSU

Desde el PC:

```bash
adb install root/KernelSU_v3.3.1_32680-release.apk
```

### G.3. Copiar los módulos al teléfono

Enviar los tres paquetes ZIP a la carpeta `Download`:

**MindTheGapps:**

Generar el paquete con MindTheGapps en [**https://magiskgapps.com**](https://magiskgapps.com/)

```bash
adb push rom-lineageos/MindTheGapps-MagiskGApps-16.0.0-arm64-20261023_034858.zip /sdcard/Download/
```

**Zygisk Next:**

```bash
adb push root/Zygisk-Next-1.5.3-880-22a1bf5-release.zip /sdcard/Download/
```

**Play Integrity Fork:**

```bash
adb push root/PlayIntegrityFork-v19.zip /sdcard/Download/
```

### G.4. Comprobar KernelSU LKM

En el teléfono:

1. Abrir la aplicación KernelSU.
2. Comprobar que aparece el estado **Funcionando**.
3. Verificar que el modo de instalación es **LKM**.

### G.5. Instalar MindTheGapps

Desde KernelSU:

`Módulos → Instalar desde almacenamiento`

Seleccionar:

```bash
MindTheGapps-MagiskGApps-16.0.0-arm64-20261023_034858.zip
```

Reiniciar el dispositivo al finalizar la instalación.

{% hint style="info" %}
Si después del reinicio aparece el mensaje «**Configuración del dispositivo se ha detenido**», realizar lo siguiente:

`Ajustes → Aplicaciones → Mostrar sistema → Configuración del dispositivo → Inhabilitar`
{% endhint %}

### G.6. Instalar Zygisk Next y Play Integrity Fork

Volver a KernelSU:

`Módulos → Instalar desde almacenamiento`

Instalar, en este orden:

**1. Zygisk Next**

```bash
Zygisk-Next-1.5.3-880-22a1bf5-release.zip
```

**2. Play Integrity Fork**

```bash
PlayIntegrityFork-v19.zip
```

Reiniciar el teléfono una vez finalizadas ambas instalaciones.

### G.7. Configurar Play Integrity Fork

Abrir KernelSU:

`Módulos → PlayIntegrityFork`

Pulsar el botón **Action** para descargar la huella válida.

### G.8. Configurar Google Play y aplicaciones bancarias

1. Iniciar sesión en Google Play Store.
2. Instalar las aplicaciones bancarias.

### G.9. Configurar el desmontaje de módulos (Umount modules)

Abrir KernelSU:

`Superusuario → [...] Tres puntos → Mostrar apps del sistema`

Comprobar que las siguientes aplicaciones tienen activada la opción **Desmontar módulos (Umount modules)**:

* Seleccionar las aplicaciones bancarias

Esta configuración permite aplicar el desmontaje de los módulos para las aplicaciones seleccionadas.

***

## 🔄 H. Actualizaciones futuras de LineageOS

### ⚠️ H.1. Consideraciones importantes

{% hint style="warning" %}
**No utilizar el actualizador OTA integrado en los Ajustes de LineageOS.**

Todas las actualizaciones deben procesarse previamente con AVBRoot, integrando el nuevo `boot.img` parcheado con KernelSU LKM y firmando nuevamente la OTA con las claves personalizadas.
{% endhint %}

El procedimiento permite actualizar LineageOS sin desbloquear el bootloader ni realizar un restablecimiento de fábrica.

### H.2. Descargar la nueva compilación oficial

Descargar la nueva compilación de [LineageOS](https://download.lineageos.org/devices/Pong/builds) y guardarla en:

```bash
rom-lineageos/
```

Ejemplo:

```bash
lineage-23.2-NUEVA-nightly-Pong-signed.zip
```

### H.3. Extraer el nuevo boot.img

Desde el PC:

```bash
./avbroot ota extract \
  --input rom-lineageos/lineage-23.2-NUEVA-nightly-Pong-signed.zip \
  --directory /tmp/new-boot \
  --partition boot
```

### H.4. Parchear `boot.img` con KernelSU LKM

Ejecutar:

```bash
./root/ksud-x86_64-unknown-linux-musl boot-patch \
  -b /tmp/new-boot/boot.img \
  -m root/lkm-aarch64-android12-5.10_kernelsu.ko \
  -o /tmp/new-boot/
```

### H.5. Firmar la nueva OTA con AVBRoot

Generar la nueva OTA integrando el `boot.img` parcheado:

```bash
./avbroot ota patch \
  --input rom-lineageos/lineage-23.2-NUEVA-nightly-Pong-signed.zip \
  --output rom-lineageos/lineage-23.2-NUEVA-avbroot-ksu.zip \
  --key-avb keys-lineageos/avb.key \
  --key-ota keys-lineageos/ota.key \
  --cert-ota keys-lineageos/ota.crt \
  --prepatched /tmp/new-boot/kernelsu_patched_*.img \
  --clear-vbmeta-flags
```

### H.6. Verificar la OTA y limpiar archivos temporales

Verificar las firmas:

```bash
./avbroot ota verify \
  --input rom-lineageos/lineage-23.2-NUEVA-avbroot-ksu.zip \
  --public-key-avb keys-lineageos/avb_pkmd.bin \
  --cert-ota keys-lineageos/ota.crt
```

Eliminar los archivos temporales:

```bash
rm -rf /tmp/new-boot
```

### H.7. Preparar KernelSU antes de actualizar

En el teléfono:

1. Abrir KernelSU.
2. Entrar en `Módulos`.
3. Desactivar temporalmente los interruptores de todos los módulos.

### H.8. Instalar la actualización mediante Recovery

Reiniciar directamente a Recovery:

```bash
adb -d reboot recovery
```

En el teléfono:

`Apply update → Apply from ADB`

Desde el PC:

```bash
adb -d sideload rom-lineageos/lineage-23.2-NUEVA-avbroot-ksu.zip
```

{% hint style="info" %}
Al tratarse de una OTA firmada con el certificado personalizado `ota.crt`, Recovery debe reconocer la firma previamente instalada y no debería solicitar una confirmación adicional de certificado.
{% endhint %}

### H.9. Finalizar la actualización

1. En Recovery, seleccionar `Reboot system now`.
2. Esperar a que LineageOS termine de arrancar.
3. Abrir KernelSU.
4. Volver a activar los módulos desactivados anteriormente.
5. Reiniciar nuevamente el teléfono.

**Actualización completada.**

***

## ✅ I. Estado final esperado

| **Componente**    | **Configuración**            |
| ----------------- | ---------------------------- |
| Dispositivo       | Nothing Phone 2 (Pong)       |
| Sistema operativo | LineageOS 23.2               |
| KernelSU          | LKM                          |
| Bootloader        | Locked                       |
| AVB               | Yellow (clave personalizada) |
| dm-verity         | Activo                       |
| Firma AVB         | Personalizada                |
| Firma OTA         | Personalizada                |
| Google Apps       | MindTheGapps Systemless      |
| Zygisk            | Zygisk Next                  |
| Play Integrity    | Play Integrity Fork          |
| Actualizaciones   | AVBRoot + KernelSU LKM       |

**Principio fundamental de mantenimiento:** conservar las claves privadas AVB y OTA originales, utilizar siempre el mismo certificado OTA y volver a parchear y firmar cada nueva compilación antes de instalarla.

[^1]: 
