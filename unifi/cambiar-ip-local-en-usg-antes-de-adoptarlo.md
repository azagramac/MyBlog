# Cambiar IP local en USG antes de adoptarlo

La red interna del USG utiliza de forma predeterminada la red 192.168.1.0/24, por defecto el USG utiliza la IP 192.168.1.1, pero si tenemos otro rango de red, ejemplo 172.16.1.0/24, tenemos un problema...\
\
La solucion pasa por configurar la IP LAN del USG antes de la adopcion a UniFi Controller.

1. Conectar el USG, la LAN a tu ordenador, no conectes la WAN!!!
2. Cambia la IP de tu ordenador a 192.168.1.100 con mascara /24
3. Abre el terminal y conecta por SSH al USG, las credenciales antes de la adopcion son:\
   user: `ubnt`\
   pass: `ubnt`

```
ssh ubnt@192.168.1.1
```

Usamos el usuario root

```
sudo su -
```

Lo primero es resetar los valores de fabrica si no lo hemos realizado.

```
set-default
```

Y reiniciara, cuando termine, volvemos acceder de nuevo y seguimos en el paso siguiente.\


Una vez reiniciado, con usuario root, habilitamos el modo configuración

```
configure
```

Eliminamos la IP actual en el puerto eth1

```
delete interfaces ethernet eth1 address 192.168.1.1/24
```

Configuramos el puerto eht1 con la nueva IP

```
set interfaces ethernet eth1 address 172.16.1.1/24
```

Aplicamos los cambios, una vez ejecutes este paso, perderas la conexion al USG

```
commit
```

Ahora cambia la IP de tu ordenador por una dentro del rango de red que has definido anteriormente, si esta todo correcto, ya puedes unirlo a tu red y adoptarlo con tu UniFi Controller.

Si haces la conexion a internet lo haces por PPPoE, asegurate de haber configurado los parametros de la WAN en UniFi Controller.\
\
:warning: Para evitar problemas de adopcion, antes de resetearlo, estando conectado a internet y a nuestro UniFi Controller, lo eliminamos desde el menu de configuracion del UniFi Controller.
