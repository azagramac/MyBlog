# Instalar fuentes OTF y TTF en Linux

<figure><img src="../.gitbook/assets/image (5) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Instalacion de las fuentes San Francisco de Apple, el formato de las fuentes es .otf y un .ttf, podemos o bien ir a la web de apple developers y descargar los .dmg que contienen las fuentes (si tenemos un Mac) o hacerlo por git...



{% hint style="info" %}
Este tutorial es valido para otras fuentes .otf y .ttf
{% endhint %}



Descargamos el pack de fuentes

```sh
git clone https://github.com/sahibjotsaggu/San-Francisco-Pro-Fonts.git && cd San-Francisco-Pro-Fonts
```

\
Con las fuentes en nuestro equipo, ahora creamos 2 directorios

```sh
sudo mkdir /usr/share/fonts/opentype/apple
sudo mkdir /usr/share/fonts/truetype/apple
```

\
Ahora copiamos las fuentes .otf

```sh
sudo cp -rf *.otf /usr/share/fonts/opentype/apple/
```

\
Y ahora las fuentes .ttf

```sh
sudo cp -rf *.ttf /usr/share/fonts/truetype/apple/
```

\
Cambiamos los permisos a las fuentes

<pre class="language-sh"><code class="lang-sh"><strong>sudo chmod 644 /usr/share/fonts/opentype/apple/*.otf
</strong><strong>sudo chmod 644 /usr/share/fonts/truetype/apple/*.ttf
</strong></code></pre>

\
Ahora tenemos que actualizar la cache del sistema

```sh
sudo fc-cache -f -v
```



Ya podemos tenemos disponible las fuentes para cambiarlas, y reiniciar el sistema.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>
