# Gnome 42.5, en Linux Mint 21.1

Usando como base Linux Mint ([Vera](https://ftp.cixug.es/mint/stable/21.1/linuxmint-21.1-cinnamon-64bit.iso)) con escritorio Cinnamon, procederemos a instalar entorno GNOME 42

<figure><img src="../.gitbook/assets/imagen (35).png" alt=""><figcaption></figcaption></figure>

Lo primero de todo es tener actualizado el sistema

```shell
sudo apt update && sudo apt upgrade -y
```

\
Si esta el sistema actualizado continuamos, si no, reinicia primero.&#x20;

Instalamos Gnome

Version minima

```sh
sudo apt install vanilla-gnome-desktop -y
```

O version completa, podemos instalar ambas

```sh
sudo apt install gnome -y
```

En un momento de la instalación, nos pedirá que gestor usar, nos dará 2 opciones, elegimos "**gdm3**", cuando termine reiniciamos el sistema. \
\
Una vez reiniciado, pulsamos en configuración abajo a la derecha, y elegimos "GNOME"

<figure><img src="../.gitbook/assets/imagen (26).png" alt=""><figcaption></figcaption></figure>

Y ya tenemos Gnome 42

<figure><img src="../.gitbook/assets/imagen (37).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (25).png" alt=""><figcaption></figcaption></figure>

En el momento de escribir este post, van por la version 42.4

Instalar Tweaks e iconos Papirus

```shell
sudo apt install gnome-tweaks papirus-icon-theme -y
```

Después de todos los cambios, reiniciar y a disfrutar!

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>
