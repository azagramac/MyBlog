# dmesg: Error al leer el búfer del kernel

Si usas mucho el dmesg, te habras dado cuenta que ya no es posible leer los mensajes del kernel con un usuario normal, y necesitas ser root (super usuario), esto se debe a la implementacion del modulo `SECURITY_DMESG_RESTRICT`, evitando que los usuarios no root puedan leer el registro del núcleo de forma predeterminada\
(`sysctl: kernel.dmesg_restrict`)

Si solo quieres poder leerlo una vez con el usuario no root

```shell
sudo sysctl kernel.dmesg_restrict=0
```

Si lo que quieres es poder consultarlo siempre desde el usuario no root, entonces el cambio debe ser persistente.

Añade al final del fichero

```shell
sudo vim /etc/sysctl.d/10-local.conf
```

esta linea

```shell
kernel.dmesg_restrict = 0
```

Y reiniciar, ahora ya podras leer los mensajes del kernel con usuarios no root.\
Siempre podras hacerlo en todo caso con sudo

```shell
sudo dmesg
```
