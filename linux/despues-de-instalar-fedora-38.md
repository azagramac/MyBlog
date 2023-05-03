---
cover: ../.gitbook/assets/Captura desde 2023-05-03 13-14-04.png
coverY: -349
---

# Después de instalar Fedora 38

Ya tenemos el sistema instalado, lo primero es actualizar los posibles paquetes.&#x20;

```sh
sudo dnf update -y
```

Si hemos actualizado paquetes y kernel, que seguramente así sea, reiniciamos. \


Instalamos las herramientas de desarrollo

```sh
sudo dnf install @development-tools
```

Cabeceras del kernel y el modulo dkms

```sh
sudo dnf install kernel-headers kernel-devel dkms coreutils
```

**Java OpenJDK**

```sh
sudo dnf install java-11-openjdk.x86_64
```

Si tu GPU es AMD, después de esto, reiniciamos.

```sh
sudo dnf install xorg-x11-drv-amdgpu mesa-va-drivers mesa-vdpau-drivers
```

**Multimedia**

```sh
sudo dnf install vlc python-vlc libaacs libbluray lame flac libdvdread transmission
```

**Utilidades**

```sh
sudo dnf install neovim git gparted vim meld cifs-utils bash-completion util-linux nmap wget curl sed tar unzip yad
```

**Gestor de contraseñas KeePass**

```sh
sudo dnf install keepassxc
```

**MEGA**

```sh
sudo dnf install https://mega.nz/linux/repo/Fedora_38/x86_64/megasync-4.9.1-1.1.x86_64.rpm
```

```sh
sudo dnf install https://mega.nz/linux/repo/Fedora_38/x86_64/nautilus-megasync-5.1.0-1.1.x86_64.rpm
```

**VirtualBox 7**

Creamos un fichero en

```sh
sudo vim /etc/yum.repos.d/virtualbox.repo
```

Y añadimos:

```sh
[virtualbox]
name=Fedora $releasever - $basearch - VirtualBox
baseurl=http://download.virtualbox.org/virtualbox/rpm/fedora/$releasever/$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://www.virtualbox.org/download/oracle_vbox_2016.asc
```

Instalamos el paquete

```sh
sudo dnf install VirtualBox-7.0
```

y finalmente damos permisos a nuestro usuario

```sh
sudo usermod -a -G vboxusers $USER
```

**Python 3**

```sh
sudo dnf install python3 python3-pip python3-pillow python3-pillow-tk
```

**Gimp**

```sh
sudo dnf install gimp gimp-help-es gimp-data-extras
```

**Kubectl**

Creamos un fichero en

```sh
sudo vim /etc/yum.repos.d/kubernetes.repo
```

Y añadimos:

```sh
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
```

finalmente, instalamos el paquete

```sh
sudo dnf install kubectl
```



Limpiar la cache&#x20;

```sh
sudo dnf clean dbcache
```

```sh
sudo dnf clean all
```
