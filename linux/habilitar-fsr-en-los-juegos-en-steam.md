# Habilitar FSR en los juegos en Steam

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Para habilitar el FSR ( FidelityFX Super REsolution ) solamente v1.0, en los juegos que usen Vulkan que tengamos en Steam, primero instalar Steam en nuestra distribucion Linux, en GNU/Linux Debian 12.1 va increiblemente genial.\
\
Al margen de la version que usemos de Proton en nuestros juegos, vamos a instalar una nueva que nos permita habilitar el FSR.&#x20;

```sh
sudo apt update && sudo apt install python3-pip -y
```

```sh
pip3 install protonup
```



Ya tenemos protonup instalado. Lo lanzamos

```sh
protonup
```

\
La primera que lo lancemos nos mostrara un mensaje asi, le decimos "y" para comenzar la instalacion.

```
Ready to download Proton-8.16-GE
Size      : 423.81 MiB
Published : 2023-09-20
Continue? (Y/N):
```

\
Luego podremos verificar que version tenemos instalada.&#x20;

```sh
$ protonup
[INFO] GE-Proton8-16 already installed
```



Ahora abirmos Steam, y seleccionamos un juego de nuestra biblioteca.

{% hint style="warning" %}
Solo es posible habilitar el FSR en juegos que usen Vulkan (incluidos DXVK y VKD3D-Proton)
{% endhint %}



Seleccionamos un juego y le damos a propiedades.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

En la ventana que se nos abre, en "Compatiblidad" seleccionamos el "GE-Proton" en mi caso es la version 8.16\


<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

en la pestaña "General" nos desplazamos al final y vemos un campo para meter parametros. \


<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Introducimos

```sh
WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_STRENGTH=1 %command%
```

&#x20;y lanzamos el juego. Si el juego tiene configuracion de FSR, como el caso del Death Stranding, no es necesario usar esta configuracion, podemos jugar con la version del Proton8.0-3 y ajustar el FSR dentro del juego.&#x20;

`WINE_FULLSCREEN_FSR` = 0 disable / 1 enabled\
`WINE_FULLSCREEN_FSR_STRENGTH` = min 0, max 5, default 2. (0 is maximum sharpness, higher values mean less sharpening)

