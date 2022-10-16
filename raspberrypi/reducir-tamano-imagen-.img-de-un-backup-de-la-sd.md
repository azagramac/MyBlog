# Reducir tamaño imagen .img de un backup de la SD

Cuando hacemos un backup de la SD a un .img, pesan demasiado... pero podemos reducir el tamaño de la .img final\
\
Primero descargamos el script

```shell
wget https://raw.githubusercontent.com/Drewsif/PiShrink/master/pishrink.sh
```

Le damos permisos de ejecución

```shell
chmod a+x pishrink.sh
```

Y lanzamos el script, pasando como parametro el fichero de la imgen .img

```shell
 sudo ./pishrink.sh imagen_raspberry.img
```

Finalmente nos dejara el .img con menos peso, para restaurar ese .img a la SD podemos usar el [RaspberryPi Imager](https://www.raspberrypi.com/software/)

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

