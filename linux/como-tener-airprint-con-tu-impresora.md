# Como tener AirPrint con tu impresora

Podríamos definir a **AirPrint** como el **servicio de impresión inalámbrica de Apple**. Gracias a él, es posible enviar aquello que deseamos imprimir de forma total y completamente inalámbrica desde un iPhone o un iPad, fácilmente a cualquier impresora compatible que se encuentre conectada a nuestra red Wi-Fi, siempre que la impresora lo soporte... y muchos fabricantes tienen modelos que no lo soportan.

![](<../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png>)

En el caso de que nuestra impresora no tenga soporte oficial a AirPrint, necesitaremos un equipo o una placa raspberry o parecida con linux.

Instalaremos una serie de paquetes, por supuesto la impresora debe estar encendida, si es por wifi, conectada a la red de casa, si es cable, conectada a la placa raspberry o similar.

```sh
sudo apt install cups cups-core-drivers printer-driver-brlaser avahi-daemon libcups2-dev python3-dev python3-cups libxml-dev -y
```

Es una instalación sencilla de cups, un servidor de impresión.\
Para entrar en el dashboard web de cups:

```
http://IP_DEL_SERVIDOR_CUPS:631
```

Continuara...

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>
