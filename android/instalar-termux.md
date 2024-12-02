# 🖥️ Instalar Termux

<img src="../.gitbook/assets/image (72).png" alt="" data-size="line">Tenemos varias posibilidades de instalar Termux en nuestro Android, pero la mas completa es directamente desde <img src="../.gitbook/assets/image (69).png" alt="" data-size="line">F-Droid ([https://f-droid.org/en/packages/com.termux/](https://f-droid.org/en/packages/com.termux/))\
\
Abrimos la App F-Droid si la tenemos instalada

![](../.gitbook/assets/Screenshot_20221115-152239_F-Droid.png)

Pinchamos donde la lupa, abajo a la derecha y buscamos Termux

![](../.gitbook/assets/Screenshot_20221115-152251_F-Droid.png)

Después de instalarse, instalaremos unos paquetes

```shell
pkg update
pkg install termux-api
pkg install termux-tools
pkg install iproute2

apt update
apt install vim curl git wget neofetch bash-completion
```

Habilitar SSH

```shell
pkg install openssh
pkg install net-tools
pkg install procps
```

Comprobamos un parametro en la configuración del ssh

```shell
cat $PREFIX/etc/ssh/sshd_config
```

Deberá estar en `yes`, `PasswordAuthentication`

Comprobaremos que usuario tenemos en Termux

```shell
whoami
```

Y le asignaremos una password a nuestro user

```shell
passwd
```

Iniciaremos el demonio SSH

```shell
sshd
```

Conectarse desde un terminal a Android&#x20;

```shell
ssh user@ip -p 8022
```

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>
