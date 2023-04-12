# Gnome 42, en Linux Mint 21

<figure><img src="../.gitbook/assets/imagen (2) (1).png" alt=""><figcaption></figcaption></figure>

Lo primero de todo es tener actualizado el sistema

```shell
sudo apt update && sudo apt upgrade -y
```

\
Si esta el sistema actualizado continuamos, si no, reinicia primero.&#x20;

Instalamos Gnome

```shell
sudo apt install gnome-core gnome-desktop-testing -y
```

En un momento de la instalación, nos pedirá que gestor usar, nos dará 2 opciones, elegimos "**gdm3**", cuando termine reiniciamos el sistema. \
\
Una vez reiniciado, pulsamos en configuración abajo a la derecha, y elegimos "GNOME"

<figure><img src="../.gitbook/assets/imagen (3) (2).png" alt=""><figcaption></figcaption></figure>

Y ya tenemos Gnome 42

<figure><img src="../.gitbook/assets/imagen (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (5).png" alt=""><figcaption></figcaption></figure>

En el momento de escribir este post, van por la version 42.4

<figure><img src="../.gitbook/assets/imagen (1).png" alt=""><figcaption></figcaption></figure>

Instalar Tweaks e iconos Papirus

```shell
sudo apt install gnome-tweaks papirus-icon-theme -y
```

Después de todos los cambios, reiniciar y a disfrutar!
