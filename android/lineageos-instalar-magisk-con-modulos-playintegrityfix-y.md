---
icon: android
---

# LineageOS, Instalar magisk con modulos PlayIntegrityFix y

<figure><img src="../.gitbook/assets/imagen.png" alt="" width="375"><figcaption></figcaption></figure>

Preparativos:

* Magisk: [https://github.com/topjohnwu/Magisk/releases/tag/v28.1](https://github.com/topjohnwu/Magisk/releases/tag/v28.1)
* PlayIntegrityFix: [https://github.com/chiteroman/PlayIntegrityFix/releases/tag/v18.7](https://github.com/chiteroman/PlayIntegrityFix/releases/tag/v18.7)
* playcurlNEXT: [https://github.com/daboynb/playcurlNEXT/releases/tag/v1.14](https://github.com/daboynb/playcurlNEXT/releases/tag/v1.14)

{% hint style="warning" %}
Pruebas realizadas con las versiones de los paquetes indicadas.
{% endhint %}

Después de instalar LineageOS en el dispositivo (OnePlus 8T en mi caso) siguiendo la [guía](https://wiki.lineageos.org/devices/kebab/install/#unlocking-the-bootloader) oficial, sin reiniciar después de instalar la rom, instalamos las [Google Apps](https://github.com/MindTheGapps/15.0.0-arm64/releases/tag/MindTheGapps-15.0.0-arm64-20250214_082511) (opcional) y el modulo [Magisk](https://github.com/topjohnwu/Magisk/releases) (renombramos el fichero .apk a .zip) y lo instalamos normalmente como la rom y las gapps.&#x20;

```shell
adb -d sideload filename.zip
```

<figure><img src="../.gitbook/assets/imagen (6).png" alt="" width="563"><figcaption></figcaption></figure>

Nada mas abrir la primera vez Magisk nos pedirá actualizar y reiniciar.&#x20;

Después de reiniciar habilitamos Zygisk, en la App de Magisk, entramos en Ajustes y marcamos Zygisk y reiniciamos de nuevo.

<figure><img src="../.gitbook/assets/imagen (2).png" alt="" width="375"><figcaption></figcaption></figure>

Ahora podemos marcar también la lista de denegación y seleccionar las apps, recomendable apps de banca, wallet, cla@vePin, Waylet, chatgpt si lo tenemos... y sobre todo los servicios de google.

* `com.google.android.gms`
* `com.google.android.unstable`
* `com.google.android.gms:snet`

Y así tendríamos que tenerlo.&#x20;

<figure><img src="../.gitbook/assets/imagen (3).png" alt="" width="375"><figcaption></figcaption></figure>

Para instalar los módulos, descargamos los ficheros .zip mencionados anteriormente, y le damos a "Instalar desde almacenamiento" en la pestaña de Módulos dentro de Magisk, seleccionamos el fichero y posteriormente reiniciamos el dispositivo.&#x20;

Los módulos instalados, (_Shamiko y Systemless Hosts aparecen deshabilitados, ya que no funcionan o al menos no me han funcionado en LinegeOS 22.1_)

<figure><img src="../.gitbook/assets/imagen (4).png" alt="" width="375"><figcaption></figcaption></figure>

Y podemos verificar que todo esta ok con la App "[SafetyNet | Integrity Checker](https://play.google.com/store/apps/details?id=com.flinkapps.safteynet)" y/o "[Play Integrity API Checker](https://play.google.com/store/apps/details?id=gr.nikolasspyr.integritycheck)"

<figure><img src="../.gitbook/assets/imagen (9).png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="danger" %}
Si te marcara error en MEETS\_STRONG\_INTEGRITY esta reportado en [https://github.com/chiteroman/PlayIntegrityFix/issues/579](https://github.com/chiteroman/PlayIntegrityFix/issues/579)
{% endhint %}
