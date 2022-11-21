# 📀 Solucion al error AACS en VLC al reproducir un BR

Si intentamos reproducir una pelicula en BluRay en el VLC, nos dará un error de AACS. \
\
Para solucionarlo.

```
sudo apt install libaacs-dev libbluray2 -y 
```

Despues nos descargamos este fichero [aacs.tar.gz](https://mega.nz/file/5JZESQhY#3AyzoPFdgpdecBFiPuag9vUWM6wpcOJcZZO0LCbKy3Y) y lo descomprimimos en `/home/$USER/.config/`&#x20;

Ahora abrimos el VLC y deberia reproducir el BR sin problemas
