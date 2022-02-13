# Habilitar DDNS en USG Security Gateway

Entorno:\
\- Unifi Controller v6.5.55\
\- USG Security Gateway, firmware v4.4.56

Entramos en nuestro UniFi Controller, y nos vamos a "Settings", en el menu lateral, una vez dentro, nos dirigimos al menu "Advanced Features", y en la parte derecha de la pantalla, "Advanced Gateway Settings"

![](../.gitbook/assets/img\_unifiDdns.png)

Una vez dentro, veremos varias opciones, click en "Create New Dynamic DNS"

![](../.gitbook/assets/img\_unifiDdns2.png)

Para configurar nuestro DDNS, en este ejemplo lo hago con el servicio [no-ip](https://www.noip.com), necesitamos:\
\- Hostname, nuestro dominio\
\- Username\
\- Password\
\- Server, en este caso, lo dejamos en blanco!

Seleccionamos el interfaz WAN, el servicio que desemos, noip en este ejemplo y rellenamos los datos.

![](../.gitbook/assets/img\_unifiDdns3.png)

Guardamos los cambios y esperamos unos minutos a que haga el aprovisionamiento.
