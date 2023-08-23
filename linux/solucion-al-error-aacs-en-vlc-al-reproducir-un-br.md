# Solución al error AACS en VLC al reproducir un BluRay

Si intentamos reproducir una película en BluRay en el VLC, nos dará un error de AACS. \
\
Para solucionarlo.

```
sudo apt install libaacs-dev libbluray2 -y 
```

Después nos descargamos este fichero [aacs.tar.gz](https://mega.nz/file/5JZESQhY#3AyzoPFdgpdecBFiPuag9vUWM6wpcOJcZZO0LCbKy3Y) y lo descomprimimos en `/home/$USER/.config/`&#x20;

Ahora abrimos el VLC y debería reproducir el BR sin problemas
