---
description: Valido también para O2
---

# Configurar USG Security Gateway para Movistar

Entorno:\
\- Unifi Controller v6.5.55\
\- USG Security Gateway, firmware v4.4.56

Entramos en nuestro UniFi Controller, y nos vamos a "Settings", en el menu lateral, una vez dentro, nos dirigimos al menu "Internet", y en la parte derecha de la pantalla, "Add Secondary Internet Connection"

![](../.gitbook/assets/img\_unifiWANMovistar.png)

Ahora rellenamos los datos.\
\
En NAME, pondremos el nombre del servicio, Telefonica, fibra.. lo que queramos, yo tengo WAN.\
\
**DNS 1:** 1.1.1.1\
**DNS 2:** 1.0.0.1

En DNS, podemos usar las que queramos, pero personalmente, no uso nunca las del operador ISP.\
\
**VLAN ID:** 6\
Importante, tiene que ser la 6 tanto para Movistar como para O2, lo que no podremos poner es la prioridad, suele ser 0 o 1, pero igualmente funciona.

Nos vamos al apartado IPv4 Connection, y del desplegable, elegiremos PPPoE\
**user:** `adslppp@telefonicanetpa`\
**pass:** `adslppp`

Guardamos los cambios y reiniciamos el USG, recuerda tener conectado el cable de red conectado al puerto WAN y este o bien a la ONT o al puerto del switch del HGU en modo bridge.

![](../.gitbook/assets/img\_unifiWANMovistar2.png)
