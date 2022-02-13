# Problemas de Vsync con AMD GPU

Si tienes problemas despues de instalar los drivers oficiales de AMD en Linux, y cuando reproduces videos sobre todo si es HD/4K o juegos los ves asi, se llama tearing.\


![](../.gitbook/assets/img\_problemVsync.png)

\
Lo primero es, si tienes los ultimos drivers, bien, si no, actualizalos antes.\
\
Creamos un fichero en

```
sudo vim /etc/X11/xorg.conf.d/20-radeon.conf
```

con el siguiente contenido y guardamos el fichero.

```
Section "Device"
    Identifier "Radeon"
    Driver "amdgpu"
    Option "TearFree" "on"
    Option "DRI" "3"
    Option "AccelMethod" "glamor"
    Option "VariableRefresh" "true"
EndSection
```

Y listo, ademas también hemos habilitado FreeSync si tu monitor es compatible, con el parametro "VariableRefresh "true", ahora solo queda reiniciar y adios al tearing.\
\
Valido en AMD GPU v21.30\
Ubuntu 20.04.2 y Linux Mint 20.2
