---
description: >-
  En entornos Linux modernos, es habitual en placas USB como Arduino cuando no
  existen reglas udev adecuadas ver el error LIBUSB_ERROR_ACCESS
icon: debian
---

# Error LIBUSB\_ERROR\_ACCESS

`LIBUSB_ERROR_ACCESS` es un error de libusb que indica que el proceso no tiene permisos suficientes para acceder al dispositivo USB a bajo nivel. Suele aparecer cuando una aplicación (Arduino IDE, dfu-util, OpenOCD, etc.) intenta abrir un dispositivo USB sin privilegios adecuados o cuando otro servicio del sistema ya lo está ocupando (frecuentemente ModemManager en dispositivos CDC/ACM).



#### Solución

📦 Instalamos los siguientes paquetes

```bash
sudo apt install -y libusb-1.0-0 dfu-util dfu-programmer
```



📝 Creamos el fichero `/etc/udev/rules.d/60-arduino.rules` con el siguiente contenido:

```bash
# ------------------------------------------------------------
# Arduino GIGA R1 WiFi (CDC ACM)
# ------------------------------------------------------------
SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0266", \
  MODE="0666", GROUP="dialout", TAG+="uaccess", ENV{ID_MM_DEVICE_IGNORE}="1", \
  ENV{ID_MM_CANDIDATE}="0"

SUBSYSTEM=="usb", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0366", \
  MODE="0666", GROUP="dialout", TAG+="uaccess", ENV{ID_MM_DEVICE_IGNORE}="1", \
  ENV{ID_MM_CANDIDATE}="0"

# ------------------------------------------------------------
# STM32 DFU (GIGA / STM32H7)
# ------------------------------------------------------------
SUBSYSTEM=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="df11", \
  MODE="0666", TAG+="uaccess", ENV{ID_MM_DEVICE_IGNORE}="1"

# ------------------------------------------------------------
# Arduino UNO
# ------------------------------------------------------------
SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0043", \
  MODE="0666", GROUP="dialout", TAG+="uaccess"

# ------------------------------------------------------------
# CH340
# ------------------------------------------------------------
SUBSYSTEM=="tty", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="7523", \
  MODE="0666", GROUP="dialout", TAG+="uaccess"
```

#### Reiniciar `daemon udev` y aplicar reglas

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

#### Verificación

```bash
udevadm info -q all -n /dev/ttyACM0 | grep MM
```

Salida esperada

```bash
$ udevadm info -q all -n /dev/ttyACM0 | grep ID_
E: ID_MM_DEVICE_IGNORE=1
E: ID_MM_CANDIDATE=1
E: ID_BUS=usb
E: ID_MODEL=Giga
E: ID_MODEL_ENC=Giga
E: ID_MODEL_ID=0266
E: ID_SERIAL=Arduino_Giga_xxxxxxxxxxxxxxxxxxxxxxxxxxxx
E: ID_SERIAL_SHORT=xxxxxxxxxxxxxxxxxxxxxxxxxxxx
E: ID_VENDOR=Arduino
E: ID_VENDOR_ENC=Arduino
E: ID_VENDOR_ID=2341
E: ID_REVISION=0101
E: ID_TYPE=generic
E: ID_USB_MODEL=Giga
E: ID_USB_MODEL_ENC=Giga
E: ID_USB_MODEL_ID=0266
E: ID_USB_SERIAL=Arduino_Giga_xxxxxxxxxxxxxxxxxxxxxxxxxxxx
E: ID_USB_SERIAL_SHORT=xxxxxxxxxxxxxxxxxxxxxxxxxxxx
E: ID_USB_VENDOR=Arduino
E: ID_USB_VENDOR_ENC=Arduino
E: ID_USB_VENDOR_ID=2341
E: ID_USB_REVISION=0101
E: ID_USB_TYPE=generic
E: ID_USB_INTERFACES=:020201:0a0000:
E: ID_USB_INTERFACE_NUM=00
E: ID_USB_DRIVER=cdc_acm
E: ID_USB_CLASS_FROM_DATABASE=Miscellaneous Device
E: ID_USB_PROTOCOL_FROM_DATABASE=Interface Association
E: ID_VENDOR_FROM_DATABASE=Arduino SA
E: ID_PATH_WITH_USB_REVISION=pci-0000:08:00.1-usbv2-0:3.3:1.0
E: ID_PATH=pci-0000:08:00.1-usb-0:3.3:1.0
E: ID_PATH_TAG=pci-0000_08_00_1-usb-0_3_3_1_0
E: ID_FOR_SEAT=tty-pci-0000_08_00_1-usb-0_3_3_1_0
```

