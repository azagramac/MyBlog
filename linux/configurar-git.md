# Configurar git

Despues de instalar git, necesitamos una configuracion minima para subir nuestro codigo a github

Lanzamos cada linea, no necesitamos permisos de root.&#x20;

```shell
git config --global credential.helper store
git config --global user.name "your name"
git config --global user.email your_email@example.com
git config --global color.diff "auto"
git config --global color.status "auto"
git config --global color.branch "auto"
git config --global color.interactive "true"
git config --global help.format "html"
```

\
Ahora vamos a generar las claves ssh

```shell
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

```shell
Generating public/private rsa key pair.
Enter file in which to save the key (/home/$USER/.ssh/id_rsa): 
Created directory '/home/$USER/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/$USER/.ssh/id_rsa.
Your public key has been saved in /home/$USER/.ssh/id_rsa.pub.
```

Copiamos nuestra clave

```shell
cat /home/$USER/.ssh/id_rsa.pub
```

\
Y lo pegamos en nuestro panel de configuracion en Github

[https://github.com/settings/ssh/new](https://github.com/settings/ssh/new)

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

Guardamos y probamos la conexion ssh

```shell
ssh -T git@github.com
```

```shell
The authenticity of host 'github.com (140.82.121.3)' can't be established.
ECDSA key fingerprint is SHA256:XXXXXXXXXXXXXXXXXXXX/XXXXXXXX/XXXXXXXX.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com,140.82.121.3' (ECDSA) to the list of known hosts.
Hi AzagraMac! You've successfully authenticated, but GitHub does not provide shell access.
```
