# Activar TRIM con systemd

![](../.gitbook/assets/img\_trimLinux.png)

Instalamos el paquete necesario para poder habilitar TRIM en nuestro equipo

```bash
sudo apt install -y util-linux
```

Con el paquette ya instalado, ahora hablitamos el servicio para que se ejecute en cada arranque.

```bash
sudo systemctl enable fstrim.timer
```

Ahora podriamos reiniciar el equipo y ya tendriamos habilitado el servicio, o bien podemos forzarlo a mano.

```bash
sudo systemctl start fstrim.timer
```

Para conocer su estado, ejecutamos

```bash
sudo systemctl status fstrim.timer
```

Y nos devolvera algo como esto, que nos indica que el servicio ya esta corriendo

```bash
$ sudo systemctl status fstrim.timer

● fstrim.timer - Discard unused blocks once a week
     Loaded: loaded (/lib/systemd/system/fstrim.timer; enabled; vendor preset: enabled)
     Active: active (waiting) since Mon 2021-08-09 07:55:41 CEST; 4h 37min ago
    Trigger: Mon 2021-08-16 00:00:00 CEST; 6 days left
   Triggers: ● fstrim.service
       Docs: man:fstrim

ago 09 07:55:41 ryzen systemd[1]: Started Discard unused blocks once a week.
```

Ya tenemos el servicio corriendo, habilitado en el incio, pero ahora vamos a forzar el TRIM, sobre todo si no lo hemos realizado nunca o hemos modificado gran cantidad de datos los ultimos dias.\
\
Con este comando, lo que le decimos, es que queremos ejecutar ahora el trim para todas las particiones

```bash
sudo systemctl enable fstrim.timer --now
```

Tambien podemos hacerlo solo a una particion en concreto

```bash
sudo fstrim -v PARTICION
```

Ejemplo, en la particion /home

```bash
sudo fstrim -v /home
```

Si queremos cambiar la frecuencia con la se ejecuta el TRIM, editamos el fichero

```bash
sudo vim /etc/systemd/system/timers.target.wants/fstrim.timer
```

La estructura por defecto del fichero es la siguiente

```lua
[Unit]
Description=Discard unused blocks once a week
Documentation=man:fstrim
ConditionVirtualization=!container

[Timer]
OnCalendar=weekly
AccuracySec=1h
Persistent=true

[Install]
WantedBy=timers.target
```

Si deseamos aumentar o reducir la frecuencia, tendriamos que cambiar el parametro "OnCalendar", ahora se ejecuta 1 vez a la semana, podemos hacer que sea un dia concreto de la semana, diario...\
el parametro Persistent, indica que el momento que el equipo se encienda en caso de estar apagado, lo ejecute al encenderlo.

Si lo que queremos es consultar la ultima vez que se ha ejecutado el analisis...

```bash
sudo systemctl list-timers fstrim.timer --all
```

y para ver el log del resultado...

```bash
sudo journalctl -u fstrim.timer
```
