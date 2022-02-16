# Crear VLAN en UniFi Controller

Entramos en nuestro UniFi Controller, y nos vamos a ajustes

![](<../.gitbook/assets/Captura de pantalla 2022-02-16 a las 21.47.37.png>)

Seleccionamos "Redes"

![](<../.gitbook/assets/Captura de pantalla 2022-02-16 a las 21.22.03.png>)

En esa pantalla veremos las redes (VLANs) creadas, ahora vamos a crear una. Click en "Crear Nueva Red"

![](<../.gitbook/assets/Captura de pantalla 2022-02-16 a las 21.22.17.png>)

Seleccionamos:

* **Finalidad:** Corporativo
* **Grupo de red:** LAN
* **VLAN:** pondremos el valor numerico que deseemos, ejemplo "10"
* **IP Puerta enlace:** rango de IP que queramos con el [CIDR](https://azagramac.gitbook.io/myblog/linux/cidr-mascaras-de-subred), ejemplo: "10.5.10.1/24"
* **Nombre de dominio:** opcional
* **Servidores de DNS:** ejemplo 1.1.1.1 y 1.0.0.1

y guardamos los cambios, esperamos a que se aprovisione y tendremos nuestra VLAN.

Ahora definiremos un puerto del switch a nuestra VLAN, nos vamos a dispositivos y pinchamos en el swtich, en nuestro caso es el US-8-150W

![](<../.gitbook/assets/Captura de pantalla 2022-02-16 a las 22.06.42.png>)

Seleccionamos "Puertos"

![](<../.gitbook/assets/Captura de pantalla 2022-02-16 a las 22.02.39.png>)

Elegimos el puerto donde vamos a tener la VLAN y lo editamos.&#x20;

![](<../.gitbook/assets/Captura de pantalla 2022-02-16 a las 22.03.19.png>)

En perfil del puerto del conmutador, elegimos la VLAN creada, en este ejemplo la VLAN es "Network NAS", lo marcamos, guardamos y esperamos a que termine de aprovisionar los cambios.&#x20;

![](<../.gitbook/assets/Captura de pantalla 2022-02-16 a las 22.03.42.png>)

Ya solo nos queda pinchar en el puerto el dispositivo.
