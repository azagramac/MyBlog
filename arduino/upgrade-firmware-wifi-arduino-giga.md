---
description: >-
  La actualización del firmware WiFi en la Arduino GIGA R1 WiFi consiste en la
  reprogramación del módulo de radio integrado (Murata CYW4343W) que gestiona
  las comunicaciones IEEE 802.11 y Bluetooth LE,
icon: wifi
---

# Upgrade Firmware WiFi Arduino GIGA

<figure><img src="../.gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

Requisitos:

* [Arduino GIGA R1](https://amzn.to/44vzSnb)
* [Arduino IDE](https://www.arduino.cc/en/software/)

\
El proceso implica cargar una imagen binaria del firmware del módulo WiFi (y sus certificados asociados) en la memoria flash del coprocesador de comunicaciones mediante un sketch (`WiFiFirmwareUpdater`) ejecutado en el entorno Arduino Mbed OS. Este sketch establece una sesión de actualización a través del bus interno entre el MCU principal y el módulo inalámbrico, verificando la versión instalada y permitiendo su sobrescritura si es necesario.



Antes de nada, instalamos el core de la placa Arduino GIGA R1 en Arduino IDE, y conectamos la placa por USB al ordenador.

<figure><img src="../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>

En Arduino IDE:\
&#xNAN;_**File > Examples > STM32H747\_System > WiFiFirmwareUpdater**_

<figure><img src="../.gitbook/assets/image (212).png" alt=""><figcaption></figcaption></figure>

Se cargara el nuevo sketch, solamente le damos a **Upload** cuando termine, nos preguntara por el monitor serial si deseamos instalar el firmware, le decimos que si "`Y`".

<figure><img src="../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

Comenzara la actualización del firmware y actualizara los certificados.

```bash
Searching for WiFi firmware file 4343WA1.BIN ...
A WiFi firmware is already installed. Do you want to install the firmware anyway? Y/[n] Y
Flashing /wlan/4343WA1.BIN file
Flashed 0%
Flashed 10%
Flashed 20%
Flashed 30%
Flashed 40%
Flashed 50%
Flashed 60%
Flashed 70%
Flashed 80%
Flashed 90%
Flashed 100%
Erasing memory mapped firmware area...
Flashing memory mapped firmware
Flashed 0%
Flashed 10%
Flashed 20%
Flashed 30%
Flashed 40%
Flashed 50%
Flashed 60%
Flashed 70%
Flashed 80%
Flashed 90%
Flashed 100%
Flashing certificates
Flashed 0%
Flashed 10%
Flashed 20%
Flashed 30%
Flashed 40%
Flashed 50%
Flashed 60%
Flashed 70%
Flashed 80%
Flashed 90%
Flashed 100%
```

Cuando termine, veremos este mensaje

```bash
Firmware and certificates updated!
It's now safe to reboot or disconnect your board.
```

Desconectamos la placa y tendremos actualizado el firmware de la wifi y los certificados ✅

