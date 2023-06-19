# Como tener AirPrint con tu impresora

Podríamos definir a **AirPrint** como el **servicio de impresión inalámbrica de Apple**. Gracias a él, es posible enviar aquello que deseamos imprimir de forma total y completamente inalámbrica desde un iPhone o un iPad, fácilmente a cualquier impresora compatible que se encuentre conectada a nuestra red Wi-Fi, siempre que la impresora lo soporte... y muchos fabricantes tienen modelos que no lo soportan.

![](<../.gitbook/assets/image (1) (1) (1) (1) (1).png>)

En el caso de que nuestra impresora no tenga soporte oficial a AirPrint, necesitaremos un equipo o una placa raspberry o parecida con linux.

Instalaremos una serie de paquetes, por supuesto la impresora debe estar encendida, si es por wifi, conectada a la red de casa, si es cable, conectada a la placa raspberry o similar.

```
sudo apt install cups cups-core-drivers libcups2-dev python3-dev python3-cups libxml-dev
```

Es una instalacion sencilla de cups, un servidor de impresion.\
Para entrar en el dashboard web de cups:

```
http://IP_DEL_SERVIDOR_CUPS:631
```

![](<../.gitbook/assets/Captura de pantalla 2022-04-07 a las 17.41.46.png>)

Continuara...
