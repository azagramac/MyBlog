# Después de Instalar Debian 12

<figure><img src="../.gitbook/assets/pngwing.com.png" alt="" width="375"><figcaption></figcaption></figure>

Nos bajamos la ISO de la web oficial de debian podéis elegir la versión stable o testing. Personalmente me quedo con la stable y luego le añado los repositorios. \
\
Grabamos la ISO y comenzamos la instalación.



<figure><img src="../.gitbook/assets/Tux.png" alt=""><figcaption></figcaption></figure>

Lo primero que vemos es que no tenemos sudo!!!

Habilitamos sudo hacemos login con root

```sh
su - root
```

Ahora habilitamos nuestro usuario con permisos de sudo

```sh
usermod -aG sudo $USER
```

y después reiniciamos el sistema.

<figure><img src="../.gitbook/assets/NicePng_linux-penguin-png_7963695.png" alt="" width="138"><figcaption></figcaption></figure>

Después de reiniciar ya tenemos nuestro sudo.&#x20;

Agregamos los repositorios `non-free` y `backports`

```sh
# Repos oficiales no libres
deb https://ftp.debian.org/debian/ bookworm contrib main non-free non-free-firmware
# deb-src https://ftp.debian.org/debian/ bookworm contrib main non-free non-free-firmware

# Actualizaciones
deb https://ftp.debian.org/debian/ bookworm-updates contrib main non-free non-free-firmware
# deb-src https://ftp.debian.org/debian/ bookworm-updates contrib main non-free non-free-firmware
deb https://ftp.debian.org/debian/ bookworm-proposed-updates contrib main non-free non-free-firmware
# deb-src https://ftp.debian.org/debian/ bookworm-proposed-updates contrib main non-free non-free-firmware

# Seguridad
deb https://security.debian.org/debian-security/ bookworm-security contrib main non-free non-free-firmware
# deb-src https://security.debian.org/debian-security/ bookworm-security contrib main non-free non-free-firmware

# Repositorios Backports
deb https://ftp.debian.org/debian/ bookworm-backports contrib main non-free non-free-firmware
# deb-src https://ftp.debian.org/debian/ bookworm-backports contrib main non-free non-free-firmware

# Multimedia
deb https://www.deb-multimedia.org bookworm main non-free
```

\
Agregamos las claves gpg

```sh
apt-key adv --keyserver keyring.debian.org --recv-keys 5C808C2B65558117
apt-key export 65558117 | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/debian-multimedia.gpg
```

\
Y actualizamos el repositorio y el sistema

<pre class="language-sh"><code class="lang-sh"><strong>sudo apt update &#x26;&#x26; sudo apt upgrade -y
</strong></code></pre>

{% hint style="info" %}
Si hemos tenido muchas actualizaciones, sobre todo alguna del kernel, recomendable reiniciar el sistema.
{% endhint %}

\
Instalación de paquetes, esta es mi selección de paquetes que tengo instalados, incluye desde librerías, programas variados, herramientas de desarrollo...

```sh
sudo apt install build-essential dkms make cmake linux-headers-$(uname -r) amd64-microcode firmware-linux-nonfree util-linux cifs-utils libfuse2 sysfsutils zlib1g-dev libbz2-dev libreadline-dev iperf3 libiperf0 apt-transport-https ca-certificates software-properties-common dirmngr gnupg openssl libssl-dev sshfs libgbm1 libgjs0g libsqlite3-dev jq libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev -y
```

y esta es mi selección que se instala desde el repositorio backports

```sh
sudo apt install -t bookworm-backports vulkan-tools mesa-utils mesa-utils-extra mesa-va-drivers mesa-vdpau-drivers mesa-vulkan-drivers mesa-opencl-icd libgl1-mesa-dri libglapi-mesa libglx-mesa0 libegl-mesa0 duf vim git curl wget nmap nvme-cli dexdump lm-sensors htop vlc libaacs0 libaacs-dev lame libbluray2 ffmpeg neofetch flac gparted meld filezilla solaar keepassxc gimp gimp-help-es gimp-data-extras v4l-utils libdvd-pkg libdvdread8 libavcodec59 papirus-icon-theme arc-theme python3 python3-pip python3-pil python3-pil.imagetk bpytop python3-psutil libglib2.0-dev-bin gir1.2-gtk-4.0 gjs libgtk-4-1 libgtk-4-bin libgtk-4-common libxatracker2 ttf-mscorefonts-installer gir1.2-gtop-2.0 p7zip-full rar unrar zip unzip bzip2 gnome-shell-extension-manager gnome-maps gnome-weather -y
```

{% hint style="info" %}
Para la instalación de paquetes de un repositorio concreto, añadimos -t y el repo

`sudo apt install -t bookworm-backports nombre_del_paquete`
{% endhint %}

\
Claves para películas en DVD

```sh
sudo dpkg-reconfigure libdvd-pkg
```

\
Configuración de `lm-sensors`

```sh
yes "" | sudo sensors-detect
```

\
Oracle Java JDK

```sh
wget -c https://download.oracle.com/java/17/archive/jdk-17.0.8_linux-x64_bin.deb
echo 'export export JAVA_HOME="/usr/lib/jvm/jdk-17-oracle-x64"' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
```

\
Google Chrome

```sh
curl -fSsL https://dl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmor | sudo tee /usr/share/keyrings/google-chrome.gpg >> /dev/null
echo deb [arch=amd64 signed-by=/usr/share/keyrings/google-chrome.gpg] http://dl.google.com/linux/chrome/deb/ stable main | sudo tee /etc/apt/sources.list.d/google-chrome.list
sudo apt update && sudo apt install -y google-chrome-stable
```

\
VirtualBox 7.0

```sh
echo deb [arch=amd64] https://download.virtualbox.org/virtualbox/debian bookworm contrib | sudo tee /etc/apt/sources.list.d/virtualbox.list
wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | sudo apt-key add -
apt-key export 2980AECF | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/virtualbox.gpg
sudo apt update && sudo apt install -y virtualbox-7.0
sudo usermod -a -G vboxusers $USER
```

\
Visual Studio Code

```sh
curl -fSsL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /usr/share/keyrings/vscode.gpg >/dev/null
echo deb [arch=amd64 signed-by=/usr/share/keyrings/vscode.gpg] https://packages.microsoft.com/repos/vscode stable main | sudo tee /etc/apt/sources.list.d/visual-studio.list
sudo apt update && sudo apt install -y code
```

\
Kubectl

```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod a+x kubectl
sudo mv kubectl /usr/bin/
```

\
Azure Cli

```sh
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

\
Pyenv

```sh
curl https://pyenv.run | bash
echo 'export PATH="$HOME/.pyenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init --path)"' >> ~/.bashrc
```

\
Firewall

```sh
sudo apt install ufw -y
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw status verbose
sudo apt install gufw
```

\
OBS Studio

```sh
sudo apt install ffmpeg obs-studio -y
```

\
OpenVPN

```sh
sudo apt install network-manager-openvpn-gnome openvpn-systemd-resolved -y
```

\
Habilitamos el servicio de TRIM

```sh
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer
```

\
Me faltarían algunas aplicacion que se instalan por .deb, como rpi-imager, cambiar firefox ESR por la version actual disponible, thunderbird (estas 2 ultimas, las instalo desde .tar.gz y luego creo un .desktop para añadir al `/home/$USER/.local/share/applications` para que se muestren en el menu de las aplicaciones, ya tengo tema para otra entrada! :smile:)



<figure><img src="../.gitbook/assets/Captura desde 2023-08-23 21-24-06.png" alt=""><figcaption></figcaption></figure>
