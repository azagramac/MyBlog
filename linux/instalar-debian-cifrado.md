# Instalar Debian cifrado

Instalacion completa de Debian en volumen cifrado con LUKS y particiones logicas

Version instalada. [Debian 12.1.0](https://cdimage.debian.org/debian-cd/current/amd64/iso-dvd/)



<figure><img src="../.gitbook/assets/pngwing.com.png" alt="" width="375"><figcaption></figcaption></figure>

Despues grabar la ISO en un soporte, sea DVD o USB, arrancamos con ella



<figure><img src="../.gitbook/assets/01.png" alt=""><figcaption></figcaption></figure>

Configuramos idioma, mapa de teclado...

<div>

<figure><img src="../.gitbook/assets/02.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/03.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/04.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/05.png" alt=""><figcaption></figcaption></figure>

</div>

Establecemos el hostname, la password de root, y nuestro usuario y password

<div>

<figure><img src="../.gitbook/assets/06.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/07.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/08.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/09.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/10.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/11.png" alt=""><figcaption></figcaption></figure>

</div>

Configuramos nuestro horario

<figure><img src="../.gitbook/assets/12.png" alt=""><figcaption></figcaption></figure>

En particionado de discos, elegiremos "Manual", nos mostrara la lista de dispositivos, lo seleccionamos y creamos una nueva tabla de particiones.&#x20;

<div>

<figure><img src="../.gitbook/assets/13.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/14.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/15.png" alt=""><figcaption></figcaption></figure>

</div>

Ya tenemos nuestro disco disponible para configurarlo.

<figure><img src="../.gitbook/assets/16.png" alt=""><figcaption></figcaption></figure>

Ahora crearemos la particion **/boot**, esta particion no va cifrada, si no no seria posible el arranque del sistema, seleccionamos el disco y le damos a "Crear una particion nueva", con 1Gb es suficiente y elegiremos de tipo **primaria**.&#x20;

<div>

<figure><img src="../.gitbook/assets/17.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/18.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/19.png" alt=""><figcaption></figcaption></figure>

</div>

La ubicacion de la particion sera al principio del disco, y luego le daremos formato Ext4, punto de montaje /boot, y ya habremos terminado con esta particion.

<div>

<figure><img src="../.gitbook/assets/20.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/21.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/22.png" alt=""><figcaption></figcaption></figure>

</div>

Vamos con el volumen cifrado, En espacio libre restante, le damos a "Crear una particion nueva", aqui elegiremos de tipo **logico**, el tamaño pondremos el total disponible, luegro dentro crearemos las particiones para la raiz / y /home.

<div>

<figure><img src="../.gitbook/assets/23 (1).png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/25 (1).png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/26 (1).png" alt=""><figcaption></figcaption></figure>

</div>

Configuramos el volumen.&#x20;

<figure><img src="../.gitbook/assets/27.png" alt=""><figcaption></figcaption></figure>

Ahora vamos a configurar el volumen cifrado

<div>

<figure><img src="../.gitbook/assets/28.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/29.png" alt=""><figcaption></figcaption></figure>

</div>

Crearemos el volumen cifrado, marcaremos el dispositivo que hemos configurado previamente

<div>

<figure><img src="../.gitbook/assets/30.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/31.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/32.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/33.png" alt=""><figcaption></figcaption></figure>

</div>

y empezara el proceso, aqui dependiendo de tu equipo llevara mas o menos tiempo.

<figure><img src="../.gitbook/assets/34.png" alt=""><figcaption></figcaption></figure>

Nos pedira la contraseña que tendremos que poner cada vez que iniciemos el sistema para desbloquear el volumen.&#x20;

{% hint style="warning" %}
Si pierdes la contraseña, pierdes el acceso a los datos.
{% endhint %}

<figure><img src="../.gitbook/assets/35.png" alt=""><figcaption></figcaption></figure>

Ya tenemos la particion /boot y el volumen cifrado, ahora nos queda crear las particiones raiz y home.

<figure><img src="../.gitbook/assets/36.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/37.png" alt=""><figcaption></figcaption></figure>

Creamos el grupo de volumen que contendra las particiones, podemos asignar el nombre que queramos el grupo de volumen.&#x20;

<div>

<figure><img src="../.gitbook/assets/38.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/39.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/40.png" alt=""><figcaption></figcaption></figure>

</div>

Creamos la particion **raiz /**, asignandole el espacio que queramos, nombre...&#x20;

<div>

<figure><img src="../.gitbook/assets/41.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/42.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/43.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/44.png" alt=""><figcaption></figcaption></figure>

</div>

Lo mismo para la particion **/home**

<div>

<figure><img src="../.gitbook/assets/45.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/46.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/47.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/48.png" alt=""><figcaption></figcaption></figure>

</div>

Ya hemos terminado de crear las particiones, en este ejemplo se han creado solamente 2, puedes crear mas, como la /var y la swap.

<figure><img src="../.gitbook/assets/49.png" alt=""><figcaption></figcaption></figure>

Ahora nos queda dar formato a las particiones creadas.&#x20;

<figure><img src="../.gitbook/assets/50.png" alt=""><figcaption></figcaption></figure>

raiz / y /home

<div>

<figure><img src="../.gitbook/assets/51.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/52.png" alt=""><figcaption></figcaption></figure>

</div>

Escribimos los cambios

<figure><img src="../.gitbook/assets/53.png" alt=""><figcaption></figcaption></figure>

Inmediatamente despues empezara la instalacion normal del sistema.

<figure><img src="../.gitbook/assets/54.png" alt=""><figcaption></figcaption></figure>

<div>

<figure><img src="../.gitbook/assets/55.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/56.png" alt=""><figcaption></figcaption></figure>

</div>

<div>

<figure><img src="../.gitbook/assets/57.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/58.png" alt=""><figcaption></figcaption></figure>

</div>

<div>

<figure><img src="../.gitbook/assets/59.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/60.png" alt=""><figcaption></figcaption></figure>

</div>

<div>

<figure><img src="../.gitbook/assets/61.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/62.png" alt=""><figcaption></figcaption></figure>

</div>

Instalacion del grub

<div>

<figure><img src="../.gitbook/assets/63.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/64.png" alt=""><figcaption></figcaption></figure>

</div>

Finalizando la instalación

<div>

<figure><img src="../.gitbook/assets/65.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/66.png" alt=""><figcaption></figcaption></figure>

</div>

Reiniciamos, ya tenemos el sistema instalado 🎉

<figure><img src="../.gitbook/assets/67.png" alt=""><figcaption></figcaption></figure>

Al inciar, nos pedira la password que hemos establecido al configurar el volumen cifrado para seguir con el arranque del sistema.

<figure><img src="../.gitbook/assets/68.png" alt=""><figcaption></figcaption></figure>

Si va todo bien, arrancara el sistema normalmente.&#x20;

<div>

<figure><img src="../.gitbook/assets/69.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/70.png" alt=""><figcaption></figcaption></figure>

</div>

detalle de como quedan las particiones creadas.

<figure><img src="../.gitbook/assets/71.png" alt=""><figcaption></figcaption></figure>

Y esto es todo, ya tienes tu Debian con la particion raiz y /home cifradas con LUKS, muy recomendable en cualquier equipo que se use hoy en dia, pero mas aun si es un equipo portatil.&#x20;

<figure><img src="../.gitbook/assets/image (15).png" alt="" width="51"><figcaption></figcaption></figure>
