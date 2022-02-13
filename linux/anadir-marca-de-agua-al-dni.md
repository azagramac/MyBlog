# Añadir marca de agua al DNI

A veces nos solicitan nuestro DNI para realizar tramites, y lo entregamos sin mas... ERROR!\
\
Con esto, añadimos una marca de agua al DNI, podemos poner "ES COPIA", "COPIA" o incluso detallar un poco a quien se lo entregamos, yo suelo poner "COPIA PARA xxxx" y añado el nombre de la empresa a quien se lo entrego.\
\
Lo primero es instalar el paquete necesario

```bash
sudo apt install imagemagick -y
```

Y ahora basta con tener nuestro DNI escaneado, vale una foto, lo dejas lo mas ajustado posible.\
\
Y para añadir la marca de agua:

```bash
convert -density 150 -fill "rgba(255,0,0,0.25)" -gravity Center -pointsize 70 -draw "rotate -45 text 0,0 'ES COPIA'" dni.jpeg  dni-marca.jpg
```

Podemos ajustar el tamaño del texto, en el parámetro

```bash
-pointsize 70
```

Mas valor, mas grande, menos, mas pequeño.

Si queremos añadir mas texto, podemos hacerlo de la siguiente manera, ejemplo especificar a quien o que va dirigido.

```bash
convert -density 150 -fill "rgba(255,0,0,0.50)" -pointsize 15 -draw "rotate -15 text 0,200 'ES COPIA'" -draw "rotate -15 text -25,260 'PARA LA EMPRESA XXXXXXXXX'" dni.jpeg  dni-marca2.jpg
```
