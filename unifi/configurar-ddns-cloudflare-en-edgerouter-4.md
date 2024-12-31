# Configurar DDNS Cloudflare en EdgeRouter 4

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

El cliente que usa el ER-4 es `ddclient`, actualmente no es compatible con la API v4 de Cloudflare, por tanto si intentas configurarlo via web o consola, no va sincronizar la IP con nuestro dominio. \
\
Requisitos:\
\- EdgeRouter 4 (valido EdgeRouter 6P) con firmware: `v2.0.9-hotfix.7`\
\- Dominio con Cloudflare\


Entramos en Cloudflare, necesitaremos crear el subdominio y obtener la global api key.&#x20;

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Seleccionamos nuestro dominio y bajamos al final de la página, y pinchamos a la derecha donde pone "**Obtenga el token de la API**"\


<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Pinchamos en "**Ver**" en "**Global API Key**"

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

nos aparecerá esta ventana, nos identificamos con nuestra clave de acceso y después nos mostrara la API KEY, la copiamos y la guardamos, que la vamos a necesitar mas tarde.\


<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Ya con la key, ahora creamos el subdominio, volvemos a la pantalla principal de Cloudflare, y ahora pulsamos en "**DNS / Registros**" en el menu lateral.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Veremos una pantalla como esta

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Pinchamos en "**Agregar registro**" y rellenamos los campos y guardamos. \
\
**Tipo:** A\
**Nombre:** el que queramos, solo nombre, ese va ser el nombre de nuestro subdominio `{subdomino}.{nuestro_dominio.com}`\
**Dirección IPv4:** Podemos poner cualquier IP, luego se va actualizar sólo cuando configuremos el ER-4\
**Estado del proxy:** Lo deshabilitamos\
**TTL:** Automático.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Ejemplo:

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Ya tenemos nuestra Global API key y nuestro subdominio creado, ahora nos vamos al router.&#x20;

Abrimos un terminal y entramos a el por SSH.&#x20;

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Ahora es copiar, cambia los datos por los tuyos y pegalos en el terminal. Cambia el interface por el que corresponda en tu caso, en el mio es el `pppoe0`.&#x20;

```sh
configure
set service dns dynamic interface pppoe0 service custom-cloudflare protocol cloudflare
set service dns dynamic interface pppoe0 service custom-cloudflare server api.cloudflare.com/client/v4
set service dns dynamic interface pppoe0 service custom-cloudflare host-name {your_subdomain.your_domain.com}
set service dns dynamic interface pppoe0 service custom-cloudflare login "your_mail_account_cloudflare"
set service dns dynamic interface pppoe0 service custom-cloudflare password "your_global_api_key"
set service dns dynamic interface pppoe0 service custom-cloudflare options "zone=your_domain.com use=web ssl=yes ttl=1"
commit ; save
```

Si nos fijamos le estamos diciendo en la parte `server`, que use la v4 del cliente de cloudflare. Y en `options`, le pasamos unos parámetros adicionales.&#x20;

Comprobar el estado de sincronización:

```bash
show dns dynamic status
```

y nos devolverá si ha ido todo bien algo asi:

```sh
interface    : pppoe0
ip address   : YOUR_PUBLIC_IP_ADDRESS
host-name    : SUBDOMAIN.DOMAIN.COM
last update  : Fri Dec 27 18:01:23 2024
update-status: good
```



Si nos volvemos a cloudlfare, veremos que ahora aparece nuestra IP pública en nuestro registro de DNS. 🎉
