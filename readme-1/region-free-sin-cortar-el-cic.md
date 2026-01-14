# Región Free, sin cortar el CIC

Sistema de autentificación de Nintendo, llamado [10NES](https://es.wikipedia.org/wiki/10NES), desarrollado para verificar los juegos y los periféricos originales.\
\
En la NES, el **CIC** (10NES) funciona como un sistema de protección que asegura que solo se puedan ejecutar cartuchos compatibles con la región de la consola. Si se introduce un cartucho de una región diferente a la de la consola, el **LED de encendido parpadea**, indicando que el juego **no es compatible**. Este parpadeo no significa que el CIC haya autenticado el juego; simplemente señala que **el juego debe coincidir con la región de la consola** para poder ejecutarse correctamente.

{% hint style="info" %}
Introducir un juego de una región diferente a la de la consola, causa que el LED encendido de la consola parpadea y muestra pantalla gris en la TV.
{% endhint %}

<figure><img src="../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

Hay varias formas de anular el chip, la mas común y mas bestia, cortar la patilla 4 del chip, bien dejándolo al aire o soldando un cable GND.&#x20;

<figure><img src="../.gitbook/assets/image (96).png" alt=""><figcaption><p>Identificación de la patilla 4</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (97).png" alt=""><figcaption><p>Dejando al aire la patilla</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (99).png" alt="" width="375"><figcaption><p>Soldando un cable, del pin 4 al pin 13</p></figcaption></figure>

la otra es retirar el chip... \
pero la menos conocida es además la reversible, el chip lo dejamos tal cual sin tocar, si tenemos buena mano con el soldador, no necesitamos ni desmontar completamente la consola.&#x20;

Solamente necesitamos un soldador de unos 30/45W, estaño, flux y 2 cables.

<figure><img src="../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Sin nombre.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (101).png" alt=""><figcaption><p>Vista con la consola montada.</p></figcaption></figure>

Solamente eso, nada mas! Ya podremos cargar juegos de otras regiones o mandos, sin que el CIC nos de problemas.&#x20;

{% hint style="info" %}
Si tu consola es PAL (50Hz), los juegos en NTSC irán igualmente a 50Hz, no a 60Hz, y viceversa.&#x20;
{% endhint %}
