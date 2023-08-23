# Información del /etc/fstab

La forma con la que Linux trata a los dispositivos es muy diferente a como lo hace Windows, en Linux todo es un archivo, `/etc/fstab` es el archivo donde se definen los diferentes puntos de montaje de particiones, discos, unidades de CD, etc...



Como cada fichero en linux, tiene su sintaxis

```sh
<partición>   <punto de montaje>   <formato>   <opciones>   <dump>   <pass>
```



Ejemplo de mi `/etc/fstab` en Debian 12

```sh
## Root & swap
/dev/mapper/debian-root				/		ext4	noatime,nodiratime,discard,errors=remount-ro	0	1
/swapfile					none		swap	sw		                0	0

# /boot was on /dev/nvme0n1p1 during installation
UUID=26d0f059-6334-4db8-835f-9c54b9955524	/boot		ext4    noatime,nodiratime,discard	0	2
UUID=4325-9d14					/boot/efi	vfat	umask=0077	                0	1

## Home
/dev/mapper/debian-home				/home		ext4	noatime,nodiratime,discard	0	2

## Optical Drive
/dev/sr0					/media/cdrom0	udf,iso9660 user,noauto	                0	0

## Synology
//nas/home/$user				/home/$user/NAS	cifs	user=$user,password="4oK/IDFLN2JVODNMdzFMeHpOMmRLV3JMcldqQTUxSERwZnl6V20=",uid=1000,gid=1000,iocharset=utf8,vers=3.0,forceuid,forcegid,noauto,x-systemd.automount,_netdev	0	0

## Folders temp in RAM
tmpfs						/tmp		tmpfs	noatime,nodiratime,nodev,nosuid,mode=1777,defaults	0	0
tmpfs						/var/tmp	tmpfs	noatime,nodiratime,nodev,nosuid,mode=1777,defaults	0	0

## No automount
UUID=df43cc4e-e6eb-8fab-878a-508a61665fcf	/media/$user	ext4	defaults,noauto	0	0
UUID=3ECA-AFFA					/media/$user	vfat	defaults,noauto	0	0
UUID=508a6166-0d9c-4a63-9972-4b46df43cc4e	/media/$user	ext4	defaults,noauto	0	0
UUID=FE4A-F2E1					/media/$user	auto	defaults,noauto	0	0
```

\
**Parámetros:**

`<partición>` define la partición o dispositivo de almacenamiento para ser montado\
`<punto de montaje>` indica el punto de montaje donde la partición será montada\
`<formato>` indica el tipo de sistema de archivos o dispositivo de almacenamiento para ser montado.&#x20;

Por ejemplo: `ext2`, `ext3`, `ext4`, `reiserfs`, `xfs`, `jfs`, `smbfs`, `iso9660`, `vfat`, `ntfs`, `swap` y `auto`. El tipo auto hace que mount determine qué tipo de sistema de archivos utiliza.

\
`<opciones>` indica las opciones de montaje.

* `auto` – El sistema de archivos será montado automáticamente durante el arranque, o cuando la orden mount -a se invoque.
* `noauto` – El sistema de archivos no será montado automáticamente, solo cuando se le ordene manualmente.
* `exec` – Permite la ejecución de binarios residentes en el sistema de archivos.
* `noexec` – No permite la ejecución de binarios que se encuentren en el sistema de archivos.
* `ro` – Monta el sistema de archivos en modo sólo lectura.
* `rw` – Monta el sistema de archivos en modo lectura-escritura.
* `user` – Permite a cualquier usuario montar el sistema de archivos. Esta opción incluye noexec, nosuid, nodev, a menos que se indique lo contrario.
* `users` – Permite que cualquier usuario perteneciente al grupo users montar el sistema de archivos.
* `nouser` – Solo el usuario root puede montar el sistema de archivos.
* `owner` – Permite al propietario del dispositivo montarlo.
* `sync` – Todo el I/O se debe hacer de forma sincrónica.
* `async` – Todo el I/O se debe hacer de forma asíncrona.
* `dev` – Intérprete de los dispositivos especiales o de bloque del sistema de archivos.
* `nodev` – Impide la interpretación de los dispositivos especiales o de bloques del sistema de archivos.
* `suid` – Permite las operaciones de suid, y sgid bits. Se utiliza principalmente para permitir a los usuarios comunes ejecutar binarios con privilegios concedidos temporalmente con el fin de realizar una tarea específica.
* `nosuid` – Bloquea el funcionamiento de suid, y sgid bits
* `noatime` – No actualiza el inode con el tiempo de acceso al filesystem. Puede aumentar las prestaciones.
* `nodiratime` – No actualiza el inode de los directorios con el tiempo de acceso al filesystem. Puede aumentar las prestaciones (véase opciones atime).
* `relatime` – Actualiza en el inode solo los tiempos relativos a modificaciones o cambios de los archivos. Los tiempos de acceso vienen actualizados solo si el último acceso es anterior respecto al de la última modificación. (Similar a noatime, pero no interfiere con programas como mutt u otras aplicaciones que deben conocer si un archivo ha sido leido después de la última modificación). Puede aumentar las prestaciones.
* `discard` – Emite las órdenes TRIM para dispositivos de bloques subyacentes cuando se liberan los bloques. Recomendado para usar si el sistema de archivos se encuentra en un SSD.
* `flush` – La opción vfat permite eliminar datos con más frecuencia, de modo que los cuadros de diálogo de copia o las barras de progreso se mantenga hasta que se hayan escrito todos los datos.
* `nofail` – Monta el dispositivo cuando está presente, pero ignora su ausencia. Esto evita que se cometan errores durante el arranque para los medios extraíbles.
* `defaults` – Asigna las opciones de montaje predeterminadas que serán utilizadas para el sistema de archivos. Las opciones predeterminadas para ext4 son: rw, suid, dev, exec, auto, nouser, async.

`<dump>` puede valer 0 ó 1 y sirve para indicar si se harán copias de seguridad con la utilidad dump\
`<pass>` sirve para indicar a fsck el orden en que los sistemas de archivos serán comprobados



Hay 3 maneras de identificar una partición o dispositivo

1. Punto de montaje, "/", "/home", swap, etc...
2. Etiquetas, LABEL=linux...
3. UUID, toda particion en Linux, va asignado un UUID que se autogenera al crear la particion.&#x20;



{% hint style="info" %}
Es posible recargar el fstab sin tener que reiniciar el sistema, `mount -a` y `umount -a`
{% endhint %}
