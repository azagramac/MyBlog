# Habilitar swap

Necesitamos tener el firmware merlin en el router, ya que nos permite habilitar la swap, ademas de un pendrive formateado en Ext4, podemos en su lugar dedicar una partición a la swap en lugar de un fichero. \
\
Creamos el fichero swap del tamaño deseado, en este ejemplo el tamaño es de 2Gb, si queremos asignar otro valor lo indicaríamos en Kilobytes

```sh
dd if=/dev/zero of=/tmp/mnt/sda1/file.swp bs=1024 count=2097152
```

<pre class="language-sh"><code class="lang-sh">## 512Mb
dd if=/dev/zero of=/tmp/mnt/sda1/file.swp bs=1024 count=524288
## 1Gb
dd if=/dev/zero of=/tmp/mnt/sda1/file.swp bs=1024 count=1048576
## 2Gb
<strong>dd if=/dev/zero of=/tmp/mnt/sda1/file.swp bs=1024 count=2097152
</strong>## 4Gb
dd if=/dev/zero of=/tmp/mnt/sda1/file.swp bs=1024 count=4194304
</code></pre>

Con el fichero ya creado, ahora vamos a configurar para que haga uso de el.&#x20;

```sh
mkswap /tmp/mnt/sda1/file.swp
```

y la activamos

```sh
swapon /tmp/mnt/sda1/file.swp
```



SI ahora hacemos un free, podemos ver que ya tenemos habilitada la swap

```sh
root@RT-AX88U_Pro:/# free
             total       used       free     shared    buffers     cached
Mem:       1018788     591072     427716       3756       5344      48180
-/+ buffers/cache:     537548     481240
Swap:      2047996          0    2047996
root@RT-AX88U_Pro:/#
```

También podemos ver su estado desde la WebUI, en Tools / SysInfo

<figure><img src="../.gitbook/assets/image (3) (2).png" alt=""><figcaption></figcaption></figure>



Pero si reiniciamos, no tendremos la swap habilitada, para evitar esto, creamos un script con permisos de ejecución y lo guardamos en `/jffs/scripts`&#x20;

```sh
#!/bin/sh

swapon /tmp/mnt/sda1/file.swp
```

y en la WebUI, habilitamos el uso de scripts.

<figure><img src="../.gitbook/assets/image (7) (2) (1).png" alt=""><figcaption></figcaption></figure>
