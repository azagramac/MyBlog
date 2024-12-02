# dmesg: Initramfs unpacking failed: Decoding failed

Si vemos el mensaje

```shell
$ sudo dmesg
[ 0.593929] Initramfs unpacking failed: Decoding failed
```

\
Editamos el ficheros

```shell
/etc/initramfs-tools/initramfs.conf
```

Cambiamos la linea `COMPRESS=lz4` to `COMPRESS=gzip`\
guardamos y aplicamos los cambios

```shell
sudo update-initramfs -u -k all
```
