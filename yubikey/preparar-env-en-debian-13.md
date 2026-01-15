# Preparar env en Debian 13

En linux disponemos de unas herramientas para sacar partido a las llave Yubikey. <br>

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**📦 Instalación de paquetes:**

```bash
sudo apt install -y gnupg2 scdaemon yubikey-manager pcscd pcsc-tools libccid
```



Habilitamos el servicio

```bash
sudo systemctl enable --now pcscd
```

output:

```bash
Synchronizing state of pcscd.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable pcscd
Created symlink '/etc/systemd/system/sockets.target.wants/pcscd.socket' → '/usr/lib/systemd/system/pcscd.socket'.
```

\
Comprobamos que se está ejecutando:

```bash
systemctl status pcscd
```

output:

```bash
● pcscd.service - PC/SC Smart Card Daemon
     Loaded: loaded (/usr/lib/systemd/system/pcscd.service; indirect; preset: enabled)
     Active: active (running) since Thu 2025-12-11 13:39:51 CET; 5s ago
 Invocation: 38a214155ba9490c89f78196ebe85bb8
TriggeredBy: ● pcscd.socket
       Docs: man:pcscd(8)
   Main PID: 5074 (pcscd)
      Tasks: 5 (limit: 76857)
     Memory: 2.2M (peak: 2.9M)
        CPU: 71ms
     CGroup: /system.slice/pcscd.service
             └─5074 /usr/sbin/pcscd --foreground --auto-exit
```



🔑 **Conectamos la Yubikey al equipo, y si ha ido bien veremo información de nuestra llave:**

```bash
ykman info
```

output:

```bash
Device type: YubiKey 5C NFC
Serial number: ********
Firmware version: 5.7.4
Form factor: Keychain (USB-C)
Enabled USB interfaces: OTP, FIDO, CCID
NFC transport is enabled

Applications	USB    	NFC    
Yubico OTP  	Enabled	Enabled
FIDO U2F    	Enabled	Enabled
FIDO2       	Enabled	Enabled
OATH        	Enabled	Enabled
PIV         	Enabled	Enabled
OpenPGP     	Enabled	Enabled
YubiHSM Auth	Enabled	Enabled
```

✅ 🎉 Bien!!! te detecta la llave perfectamente!



**⚙️ Personalización**

```bash
gpg --card-status
```

output:

```bash
Reader ...........: Yubico YubiKey OTP FIDO CCID 00 00
Application ID ...: D********************************
Application type .: OpenPGP
Version ..........: 3.4
Manufacturer .....: Yubico
Serial number ....: ********
Name of cardholder: [no establecido]
Language prefs ...: [no establecido]
Salutation .......: 
URL of public key : [no establecido]
Login data .......: [no establecido]
Signature PIN ....: no forzado
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 0
KDF setting ......: off
UIF setting ......: Sign=off Decrypt=off Auth=off
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]
```

Si has llegado a este punto y no has tenido problemas, tienes lo necesario para administrar la llave en Linux.

{% hint style="danger" %}
Es posible que te de problemas gpg --card-status
{% endhint %}

Si al ejecutar:

```bash
gpg --card-status
```

Te sale este output:

```bash
gpg: selecting card failed: Service is not running
gpg: tarjeta OpenPGP no disponible: Service is not running
```

Ejecuta:

```bash
gpgconf --kill gpg-agent
gpgconf --launch gpg-agent
gpg --card-status
```

y ya deberia solucionar el problema. &#x20;
