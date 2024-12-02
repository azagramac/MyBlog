# Instalar GNU/Linux Debian 12, en Macbook Pro (late 2015) A1502

La ultima version disponilbe para este equipo es MacOS Monterey, con lo que aun teniendo hardware para seguir dandole vida, Apple ya lo ha jubilado, pero le podemos dar otra vida con GNU/Linux

{% hint style="warning" %}
Antes de formatear, asegurate de tener una copia MacOS por si decides volver
{% endhint %}

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (35).png" alt="" width="525"><figcaption></figcaption></figure>

La instalacion se hace normalmente, lo unico que debes pulsar la tecla "Alt" antes de que aparezca la manzana, para que te muestre el menu de arranque, asi puedes elegir el USB/CD/DVD donde tengas grabada la ISO de Debian.

<figure><img src="../.gitbook/assets/image (37).png" alt="" width="335"><figcaption></figcaption></figure>

{% hint style="info" %}
Este tutorial es para Debian 12.1.0, pero puedes instalar cualquier otra distribución
{% endhint %}

y realizamos la instalacion normalmente, aunque el trackpad NO FUNCIONA en la instalacion, conecta un raton USB



Se ve todo muy pequeño, debido a la resolucion de la pantalla retina, no te preocupes que cuando termine se ve bien, de lujo ademas!

<figure><img src="../.gitbook/assets/image (38).png" alt="" width="563"><figcaption></figcaption></figure>



Cuando termine de instalar, aun nos queda un poco de trabajo que hacer, para tenerlo listo.

<figure><img src="../.gitbook/assets/image (39).png" alt="" width="563"><figcaption></figcaption></figure>

Bien, con Debian instalado, lo priemero es habilitar el sudo para nuestro usuario.

```sh
su - root
```

```sh
usermod -aG sudo $USER
```

y reiniciamos el macbook



Tenemos que instalar unos paquetes...

```sh
sudo apt update && sudo apt install build-essential dkms make cmake linux-headers-$(uname -r) intel-microcode firmware-linux-nonfree util-linux firmware-brcm80211 git kmod libssl-dev checkinstall -y
```

y reiniciamos de nuevo.&#x20;



Ahora debemos descargarnos un fichero y copiarlo en `/lib/firmware/brcm`

```sh
wget https://gist.githubusercontent.com/MikeRatcliffe/9614c16a8ea09731a9d5e91685bd8c80/raw/38180b6a0ce552e1a3a2826ffea2bf1f52d05e9f/brcmfmac43602-pcie.txt
```

\
y lo copiamos

```sh
sudo cp -rf brcmfmac43602-pcie.txt /lib/firmware/brcm/
```



Ahora aplicamos un parche para suspension.&#x20;

```sh
sudo vim /lib/systemd/system-sleep/lid_wakeup_disable
```

```sh
#!/bin/sh

# /lib/systemd/system-sleep/lid_wakeup_disable
#
# Avoids that system wakes up immediately after suspend or hibernate
# with lid open (e.g. suspend/hibernate through KDE menu entry)
#
# Tested on MacBookPro12,1

case $1 in
  pre)
    if cat /proc/acpi/wakeup | grep -qE '^LID0.*enabled'; then
        echo LID0 > /proc/acpi/wakeup
    fi
    ;;
esac
```

le damos permisos de ejecución

```sh
sudo chmod a+x /lib/systemd/system-sleep/lid_wakeup_disable
```



Otro script para levantar la wifi despues de una suspensión

```sh
sudo vim /usr/lib/systemd/system-sleep/network_hack_hibernation
```

```sh
#!/bin/sh

# /usr/lib/systemd/system-sleep/network_hack_hibernation
#
# Restores network controller functionality after wakeup from
# hibernation
#
# Tested on MacBookPro12,1
# BCM43602 WiFi network controller

if [ "$2" = "hibernate" ]; then
    case "$1" in
        pre)
            if lsmod | grep -q brcmfmac; then
                rmmod brcmfmac
            fi
            ;;
        post)
            modprobe brcmfmac
            ;;
    esac
fi
```

le damos permisos de ejecución

```sh
sudo chmod a+x /lib/systemd/system-sleep/network_hack_hibernation
```

y reiniciamos

\
😁

<figure><img src="../.gitbook/assets/Captura desde 2023-08-27 09-49-05.png" alt=""><figcaption></figcaption></figure>

\


camara iSight

{% hint style="danger" %}
No he conseguido que funcione normalmente y menos con Cheese, si lo consigues... avisa!
{% endhint %}

```sh
git clone https://github.com/patjak/facetimehd-firmware.git && cd facetimehd-firmware
```

```sh
make
sudo make install
sudo ls /lib/firmware/facetimehd/firmware.bin

sudo depmod
sudo modprobe facetimehd
```

y reiniciamos, en Cheese no funciona, da error de que no encuentra la camara, he conseguido que funcione con mplayer, pero no es util si pretendes hacer videollamadas con Teams, Telegram o Meet por ejemplo.&#x20;
