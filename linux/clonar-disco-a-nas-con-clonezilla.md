---
description: Como clonar un disco duro/ssd/nvme a un NAS por medio de samba
---

# Clonar disco a NAS con CloneZilla

<figure><img src="../.gitbook/assets/imagen.png" alt="" width="375"><figcaption></figcaption></figure>

Lo primero, tenemos que crear un USB o CD, con CloneZilla, nos descargamos la ISO\
[https://clonezilla.org/downloads/download.php?branch=alternative](https://clonezilla.org/downloads/download.php?branch=alternative) ( ⚠️ elegir ISO)

Ya teniendo el USB/CD creado con CloneZilla, arrancamos el equipo con el.

<figure><img src="../.gitbook/assets/imagen (1).png" alt="" width="563"><figcaption></figcaption></figure>

Configuramos el idioma y el idioma del teclado

![](../.gitbook/assets/PXL\_20230820\_012533450.jpg)![](../.gitbook/assets/PXL\_20230820\_012544007.jpg)![](<../.gitbook/assets/PXL\_20230820\_012606327 (1).jpg>)![](<../.gitbook/assets/PXL\_20230820\_012617152 (1).jpg>)



Comenzamos

<figure><img src="../.gitbook/assets/imagen (2).png" alt="" width="563"><figcaption></figcaption></figure>

Elegiremos la primera opción "**device-image**", ya que vamos a clonar nuestro disco a una imagen

<figure><img src="../.gitbook/assets/imagen (3).png" alt="" width="563"><figcaption></figcaption></figure>

Aquí, como vamos a clonar el disco a nuestro NAS, elegiremos el tipo de conexion, en mi caso "**samba\_server**", si prefieres hacerlo a un disco externo por USB, elije la primera opción.&#x20;

<figure><img src="../.gitbook/assets/imagen (4).png" alt="" width="563"><figcaption></figcaption></figure>

Configuramos el metodo de conexion de red de la maquina donde tenemos el disco que queremos clonar.

<figure><img src="../.gitbook/assets/imagen (5).png" alt="" width="563"><figcaption></figcaption></figure>

Y aquí, configuramos la IP de nuestro NAS, la ruta donde vamos a guardar la imagen y el protocolo samba que vamos a usar. (si no lo conoces, déjalo en auto)

![](<../.gitbook/assets/imagen (6).png>)![](<../.gitbook/assets/imagen (7).png>)![](<../.gitbook/assets/imagen (8).png>)



La parte de seguridad por defecto en auto. Después nos pedirá nuestra password del NAS para montar el volumen.&#x20;

![](<../.gitbook/assets/imagen (9).png>) ![](<../.gitbook/assets/imagen (10).png>)



La siguiente opción, déjalo por defecto.&#x20;

<div align="center">

<figure><img src="../.gitbook/assets/imagen (11).png" alt="" width="375"><figcaption></figcaption></figure>

</div>



{% hint style="info" %}
En caso de tener que restaurar, es este punto donde cambia para poder restaurar nuestra imagen a disco.
{% endhint %}

Aquí le indicamos que queremos clonar, si queremos hacer una copia del disco, completamente elegiremos la primera opción "**savedisk**", si por el contrario queremos guardar las particiones, elegiremos la segunda opción "**saveparts**", en caso de tener que restaurar un disco, realizaremos los pasos anteriores hasta el momento, y aquí seleccionaríamos "**restoredisk**" o "**restoreparts**" según hayamos elegido en el momento de clonar. \
\
En este caso, "**savedisk**" ya que queremos hacer una clonación completa del disco

<figure><img src="../.gitbook/assets/imagen (12).png" alt=""><figcaption></figcaption></figure>

Aquí le indicamos el nombre de la imagen que vamos a crear a partir de nuestro disco.\
( en caso de tener que restaurar, nos mostraria una lista de las imagenes que tengamos y seleccionariamos la que queramos)

<figure><img src="../.gitbook/assets/imagen (13).png" alt="" width="563"><figcaption></figcaption></figure>

En este punto, nos lista los discos disponibles que tenemos en nuestro equipo, seleccionamos el que vamos a clonar a una imagen.&#x20;

<figure><img src="../.gitbook/assets/imagen (14).png" alt="" width="563"><figcaption></figcaption></figure>

El metodo de compresión de la imagen, lo dejaremos en `-z1p`.&#x20;

<figure><img src="../.gitbook/assets/imagen (15).png" alt="" width="563"><figcaption></figcaption></figure>

Aquí, si hemos realizado un `fsck` antes de la clonación no hace falta, en caso contrario, selecciona la 2ª opcion "`fsck -y`"

<figure><img src="../.gitbook/assets/imagen (16).png" alt="" width="563"><figcaption></figcaption></figure>

En esta parte, seleccionamos que SI, queremos una imagen que podamos restaurar en caso necesario, si no de que queremos la imagen? Esto comprueba que la imagen creada es valida para restaurarla, si al crearla diera error, nos tocaría volver a crearla de nuevo.&#x20;

<figure><img src="../.gitbook/assets/imagen (17).png" alt="" width="563"><figcaption></figcaption></figure>

Cifrado de la imagen. Esto es al gusto de cada uno, personalmente siempre prefiero cifrarlas.

{% hint style="warning" %}
Si disco esta cifrado con LUKS, no cifres la imagen, ya que da problemas a la hora de restaurar el disco.
{% endhint %}

<figure><img src="../.gitbook/assets/imagen (18).png" alt="" width="563"><figcaption></figcaption></figure>

Esta parte es que quieres que haga el sistema cuando acabe de crear la imagen, lo dejamos por defecto.&#x20;

<figure><img src="../.gitbook/assets/imagen (19).png" alt="" width="563"><figcaption></figcaption></figure>

Y listo! Hace una comprobacion y nos saca un informe como este y nos pregunta que si queremos proceder, le decimos que si "Y"

<figure><img src="../.gitbook/assets/imagen (20).png" alt=""><figcaption></figcaption></figure>

y comienza a clonar... Aqui dependiendo de la velocidad de lectura de tu disco a clonar, tu red, tu velocidad del NAS, tardara mas o menos, en mi caso unos 7 minutos aprox en clonar un SSD de 1Tb al NAS por red gigabit, ten en cuenta que no clona los espacios vacíos, solo clona la parte de datos y ademas la comprime, en mi caso la suma total de todas las particiones rondaba los 90GB y comprimidos apenas eran 6Gb el tamaño final de la imagen.&#x20;

<figure><img src="../.gitbook/assets/imagen (21).png" alt="" width="563"><figcaption></figcaption></figure>

Cuando termine, verificara que las imagenes son restaurables, si no ha dado ningun error, nos mostrara una ventana asi.&#x20;

<figure><img src="../.gitbook/assets/imagen (22).png" alt="" width="563"><figcaption></figcaption></figure>

Y listo!&#x20;

<figure><img src="../.gitbook/assets/imagen (23).png" alt="" width="563"><figcaption></figcaption></figure>

Detalle de la imagen clonada de Debian 12, cifrado con LUKS.

<figure><img src="../.gitbook/assets/imagen (24).png" alt="" width="452"><figcaption></figcaption></figure>
