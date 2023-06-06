# Error Adopción Pendiente en USG Security Gateway

Pongamos que hemos adoptado con exito nuestro USG en nuestro UniFi Controller, tenemos nuestro USG en nuestro site.. ok.\
\
Pero pongamos que ahora queremos llevarnoslo a otro site dentro de nuestra cuenta de UniFi, hemos adquirido un Cloud Key o hemos instalado UniFi Controller en otra maquina...

Si queremos adoptarlo, nos mostrara un mensaje de adopcion pendiente, y nos saldra un menu de adopcion avanzada, que si entramos en ella, nos muestra la IP de nuestro USG, y en blanco username y password, si has intentado, no es ubnt/ubnt, ni root/ubnt...

Tenemos que irnos al primer UniFi Controller (por tu bien que tengas acceso al si no, no quedara otra que restaruar el USG y volver adoptarlo desde 0)

Entramos en el primer UniFi Controller, y nos vamos a "Settings" y bajamos al final, en el apartado "Application Configuration"

![](../.gitbook/assets/img\_UnifiErrorAdoption.png)

Si nos vamos al final de la nueva pantalla, veremos al final la opcion "Device SSH Authentication", desplegamos y veremos un nombre de usuario y una password, esas credenciales son las que debemos meter en la parte de Adopcion Avanzada, y esperar, ojo!! No tengas a la vez los 2 UniFi Controller iniciados en tu red.

![](../.gitbook/assets/img\_UnifiErrorAdoption2.png)

Si seguimos con problemas de adopcion, debemos entrar por ssh en el USG con las credenciales y ejecutar este comando

```sh
set-inform IP_UNIFI_CONTROLLLER
```

Como ultimo caso, deberas restaurarlo desde 0 por medio del boton reset (2) presionandolo durante 10s.&#x20;

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>
