# Configurar WireGuard en EdgeRouter 4 / 6P

<figure><img src="../.gitbook/assets/imagen (1).png" alt=""><figcaption></figcaption></figure>

Vamos a configurar la VPN de WireGuard en el EdgeRouter 6P /  EdgeRouter 4

{% hint style="info" %}
Realizado en firmware **v2.0.9-hotfix.7,** en la nueva version v3.0.0 incluye de serie wireguard, los comandos son los mismos.
{% endhint %}



Primero de todo, nos conectamos por terminal al router

<pre class="language-sh"><code class="lang-sh">$ ssh ubnt@ip_router
  _____    _            
 | ____|__| | __ _  ___          (c) 2010-2020
 |  _| / _  |/ _  |/ _ \         Ubiquiti Networks, Inc.
 | |__| (_| | (_| |  __/         
 |_____\__._|\__. |\___|         https://www.ubnt.com
             |___/

Welcome to EdgeOS

By logging in, accessing, or using the Ubiquiti product, you
acknowledge that you have read and understood the Ubiquiti
License Agreement (available in the Web UI at, by default,
http://192.168.1.1) and agree to be bound by its terms.

<strong>ubnt@XXX.XXX.XXX.XXX's password: 
</strong>Linux EdgeRouter-4 4.9.79-UBNT #1 SMP Thu Jun 15 11:34:36 UTC 2023 mips64
Welcome to EdgeOS
ubnt@EdgeRouter-4:~$
</code></pre>



Con el fin de tener todo organizado, creamos una carpeta de nombre `wireguard` en `/home/$user`

```sh
mkdir wireguard && cd wireguard
```



### Descargar e instalar wireguard

Ahora, descargamos el paquete de wireguard para el EdgeRouter 4 (válido para el 6P, también hay otros modelos disponibles)

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt="" width="128"><figcaption></figcaption></figure>

Repositorio oficial de WireGuard para EdgeOS: [https://github.com/WireGuard/wireguard-vyatta-ubnt/releases](https://github.com/WireGuard/wireguard-vyatta-ubnt/releases)

<pre class="language-sh"><code class="lang-sh"><strong>curl -OL https://github.com/WireGuard/wireguard-vyatta-ubnt/releases/download/1.0.20220627-1/e300-v2-v1.0.20220627-v1.0.20210914.deb
</strong></code></pre>



Ya con el paquete descargado, lo instalamos

```sh
sudo dpkg -i e300-v2-v1.0.20220627-v1.0.20210914.deb
```

y verificamos la instalación

```sh
ubnt@EdgeRouter-4:~$ sudo wg --version
wireguard-tools v1.0.20210914 - https://git.zx2c4.com/wireguard-tools/
```



### Generar claves

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt="" width="128"><figcaption></figcaption></figure>

Generamos las clave privada y pública y tambien la preshared-key

```sh
wg genkey | tee /config/auth/wireguard.key | wg pubkey >  /config/auth/wireguard.pub
wg genpsk | tee /config/auth/wireguard.psk
```

Comprobamos las claves

```sh
ubnt@EdgeRouter-4:~$ ls -l /config/auth/
total 12
-rw-r--r--    1 ubnt     vyattacf        45 Dec 31 02:13 wireguard.key
-rw-r--r--    1 ubnt     vyattacf        45 Dec 31 02:13 wireguard.psk
-rw-r--r--    1 ubnt     vyattacf        45 Dec 31 02:13 wireguard.pub
```



### Configuracion parte servidor.&#x20;

Ahora definiremos las reglas del firewall.

```sh
configure
set firewall name WAN_LOCAL rule 30 description 'Allow WireGuard'
set firewall name WAN_LOCAL rule 30 action accept
set firewall name WAN_LOCAL rule 30 protocol udp
set firewall name WAN_LOCAL rule 30 destination port 51820
commit ;save
```

{% hint style="success" %}
El puerto `51820`, es por defecto, pero por seguridad mejor utiliza otro.&#x20;
{% endhint %}

### Configurar Interface `wg0`

```sh
configure
set interfaces wireguard wg0 description "WireGuard"
set interfaces wireguard wg0 private-key /config/auth/wireguard.key
set interfaces wireguard wg0 address 10.1.1.1/24
set interfaces wireguard wg0 listen-port 51820
set interfaces wireguard wg0 route-allowed-ips false
commit ;save
```

{% hint style="warning" %}
En `address`, introduce la IP que quieras usar como servidor de wireguard, y asegurate de seleccionar el puerto que has definido en el paso anterior en `destination port`.
{% endhint %}

{% hint style="info" %}
Te habrás fijado que la opción `route-allowed-ips` está en `false`, es porque vamos a establecer acceso a la LAN.&#x20;
{% endhint %}



### Configuración de peers, tantos como necesites.

Ya tenemos la parte servidor configurado, ahora toca generar los clientes (peers).&#x20;



Generamos la clave privada y pública de nuestra peer, (aquí podemos generar tantas como queramos.)

```bash
wg genkey | tee /home/ubnt/wireguard/peer1.key | wg pubkey > /home/ubnt/wireguard/peer.pub
```



Si queremos generar mas, tan solo cambiamos el 1 por un 2 por ejemplo y listo:

```bash
wg genkey | tee /home/ubnt/wireguard/peer2.key | wg pubkey > /home/ubnt/wireguard/peer2.pub
```



Necesitaremos la clave privada (`peerX.key`) que usaremos luego para configurar nuestra App.&#x20;

Hacemos un cat a nuestra clave pública de nuestra peer

```bash
cat /home/ubnt/wireguard/peerX.pub
```

y copiamos nuestra clave.&#x20;



Ahora configuraremos nuestro peer, adaptarlo a vuestra configuración.&#x20;

<pre class="language-sh"><code class="lang-sh"><strong>configure
</strong>set interfaces wireguard wg0 peer {{ peer1 pub key }} description "Android"
set interfaces wireguard wg0 peer {{ peer1 pub key }} allowed-ips 10.1.1.2/32
set interfaces wireguard wg0 peer {{ peer1 pub key }} endpoint {{ your_sub_domain.domain.com }}:51820
set interfaces wireguard wg0 peer {{ peer1 pub key }} persistent-keepalive 25
set interfaces wireguard wg0 peer {{ peer1 pub key }} preshared-key /config/auth/wireguard.psk
commit ;save
</code></pre>

{% hint style="warning" %}
WireGuard usa `allowed-ips` también como **tabla de enrutamiento, n**o puede haber dos peers con la **misma red exacta** en `allowed-ips`.

Si lo haces (ejemplo: ambos tienen `0.0.0.0/0` y `10.1.1.0/24`), WireGuard no sabe a qué peer enviar ese tráfico → normalmente se asigna al **último peer configurado**, y el otro queda “aislado”.
{% endhint %}

En el cliente, ya le asignas las redes a las cuales quieres tener acceso, si son VLANs, recuerda que debes configurarlas previamente en el router.

{% hint style="info" %}
`endpoint {{ your_sub_domain.domain.com }}:51820`\
Aqui pon el mismo puerto que estableciste antes en la parte servidor.&#x20;
{% endhint %}

\
Realizaremos estos pasos, tantas veces como peers queramos poner, recuerda siempre poner una pub key diferente a cada peer y la IP, que no tengan varios peers, misma IP.&#x20;



Entramos en el interfaz web del router, y veremos nuestro nuevo interfaz.&#x20;

<figure><img src="../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

###

### Configuración para cliente WireGuard



Creamos un fichero de texto que guardaremos como `peer.conf`

```yaml
[Interface]
Address = {{ IP definido antes para el peer }}
DNS = 1.1.1.1,1.0.0.1     # Si tienes un AdGuard Home o PiHole, puedes poner su IP
ListenPort = 51820        # Puerto que hayas definido en la parte servidor
PrivateKey = {{ clave /home/ubnt/wireguard/peer.key }}

[Peer]
AllowedIPs = 0.0.0.0/0, 192.168.1.0/24, 172.16.1.0/24    # Rangos de IP que quieres acceder
Endpoint = {{ your_sub_domain.domain.com }}:51820
PersistentKeepalive = 25
PreSharedKey = {{ clave /config/auth/wireguard.psk }}
PublicKey = {{ clave /config/auth/wireguard.pub }}
```

###

### Configurar cliente App WireGuard

Nos descargamos la App para nuestro dispositivo. [Google Play](https://play.google.com/store/apps/details?id=com.wireguard.android) o  [App Store](https://apps.apple.com/es/app/wireguard/id1441195209)

Creamos una nueva configuración desde 0.&#x20;

* **Nombre:** El que queramos.&#x20;
* **Clave privada:** Pegamos el contenido de `/home/ubnt/wireguard/peer.key`
* **Clave pública:** Se rellena automáticamente al poner la privada. (Que coincidirá con el `peer.pub`)
* **Direcciones:** Una IPv4 del rango que definimos "[Configurar interface](configurar-wireguard-en-edgerouter-4-6p.md#configurar-interface)", en este tutorial `10.1.1.1/24`, pero no la `10.1.1.1,` que esa es reservada del wireguard, elige otra dentro del rango.
* **Puerto:** El que hemos definimos anteriormente.&#x20;
* **Servidor DNS:** podemos usar uno público, ejemplo `1.1.1.1,1.0.0.1` de Cloudflare, de Google `8.8.8.8,8.8.4.4` o mejor... si tenemos montado un [AdGuardHome](https://github.com/azagramac/adguardhome-docker) o un PiHole, podemos poner la IP del host donde lo tenemos, así evitamos tener publicidad también por la VPN 😎
* **MTU:** lo dejamos en auto

<figure><img src="../.gitbook/assets/image (154).png" alt="" width="375"><figcaption></figcaption></figure>

Le añadimos un Par.&#x20;

* **Clave pública:** Pegamos el contenido de `/config/auth/wireguard.pub`
* **Clave precompartida:** Pegamos el contenido de `/config/auth/wireguard.psk`
* **Keepalive:** El tiempo que definimos anteriormente en "[Configuración de Peers](configurar-wireguard-en-edgerouter-4-6p.md#configuracion-de-peers)"
* **EndPoint:** EL dominio y puerto, el puerto es el definido en "[Definir reglas](configurar-wireguard-en-edgerouter-4-6p.md#definir-reglas)", `{domain}:{port}` que tengamos configurado para poder acceder al el, si tenemos un DDNS, lo pondremos en este campo.&#x20;
* **IPs permitidas:** Aqui pondremos los rangos separados por "`,`" que queramos tener acceso, en lugar de poner el comodin `0.0.0.0/0`, podemos poner el rango de nuestra LAN, por ejemplo `192.16.1.0/24`, la que definimos en "[Configuración de peers](configurar-wireguard-en-edgerouter-4-6p.md#configuracion-de-peers)"

<figure><img src="../.gitbook/assets/image (155).png" alt="" width="375"><figcaption></figcaption></figure>

### Resultados

<figure><img src="../.gitbook/assets/image (156).png" alt="" width="375"><figcaption></figcaption></figure>

En esta captura, vemos como conectado en la red móvil, con la VPN, filtramos la publicidad y además tenemos habilitado DoT[^1] y DoH[^2]. 😎

<figure><img src="../.gitbook/assets/image (158).png" alt="" width="563"><figcaption></figcaption></figure>

### Test de velocidad.

* **Red movil:** 4G
* **Operador:** Simyo
* **VPN:** Conectado

<figure><img src="../.gitbook/assets/image (8).png" alt="" width="375"><figcaption></figcaption></figure>



Si queremos ver desde el router el estado de los peers

```bash
$ sudo wg show
interface: wg0
  public key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  private key: (hidden)
  listening port: 51820

peer: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  preshared key: (hidden)
  endpoint: xxx.xxx.xxx.xxx:48735
  allowed ips: 10.1.1.2/32
  latest handshake: 28 minutes, 15 seconds ago
  transfer: 742.85 MiB received, 988.86 MiB sent
  persistent keepalive: every 25 seconds

peer: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  preshared key: (hidden)
  endpoint: xxx.xxx.xxx.xxx:51820
  allowed ips: 10.1.1.3/32
  transfer: 0 B received, 44.08 KiB sent
  persistent keepalive: every 25 seconds

peer: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  preshared key: (hidden)
  endpoint: xxx.xxx.xxx.xxx:51820
  allowed ips: 10.1.1.4/32
  transfer: 0 B received, 44.37 KiB sent
  persistent keepalive: every 25 seconds
```



y para ver una lista de los comandos ejecutados:

```bash
$ show configuration commands | grep wireguard
set interfaces wireguard wg0 address 10.1.1.1/24
set interfaces wireguard wg0 description WireGuard
set interfaces wireguard wg0 listen-port 51820
set interfaces wireguard wg0 mtu 1420
set interfaces wireguard wg0 peer {{ peer2 pub key }} allowed-ips 10.1.1.3/32
set interfaces wireguard wg0 peer {{ peer2 pub key }} description 'MacBook Pro'
set interfaces wireguard wg0 peer {{ peer2 pub key }} endpoint 'xxxxxxxxxxxxxxxxxxxx:51820'
set interfaces wireguard wg0 peer {{ peer2 pub key }} persistent-keepalive 25
set interfaces wireguard wg0 peer {{ peer2 pub key }} preshared-key /config/auth/wireguard.psk
set interfaces wireguard wg0 peer {{ peer3 pub key }} allowed-ips 10.1.1.4/32
set interfaces wireguard wg0 peer {{ peer3 pub key }} description 'NothingPhone'
set interfaces wireguard wg0 peer {{ peer3 pub key }} endpoint 'xxxxxxxxxxxxxxxxxxxx:51820'
set interfaces wireguard wg0 peer {{ peer3 pub key }} persistent-keepalive 25
set interfaces wireguard wg0 peer {{ peer3 pub key }} preshared-key /config/auth/wireguard.psk
set interfaces wireguard wg0 peer {{ peer1 pub key }} allowed-ips 10.1.1.2/32
set interfaces wireguard wg0 peer {{ peer1 pub key }} description 'OnePlus 8T'
set interfaces wireguard wg0 peer {{ peer1 pub key }} endpoint 'xxxxxxxxxxxxxxxxxxxx:51820'
set interfaces wireguard wg0 peer {{ peer1 pub key }} persistent-keepalive 25
set interfaces wireguard wg0 peer {{ peer1 pub key }} preshared-key /config/auth/wireguard.psk
set interfaces wireguard wg0 private-key /config/auth/wireguard.key
set interfaces wireguard wg0 route-allowed-ips false

$ show interfaces wireguard
Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down
Interface    IP Address                        S/L  Description                 
---------    ----------                        ---  -----------                 
wg0          10.1.1.1/24                      u/u  WireGuard
```



A disfrutar! 😊

[^1]: DNS sobre TLS, o DoT, es un estándar para encriptar las consultas de DNS y mantenerlas seguras y privadas. DoT utiliza el mismo protocolo de seguridad, TLS, que usan los sitios web HTTPS para encriptar y autenticar las comunicaciones. (TLS también se conoce como "[ SSL](https://www.cloudflare.com/learning/ssl/what-is-ssl/).") DoT añade la encriptación TLS sobre el protocolo de datagrama de usuarios (UDP), que se utiliza para las consultas de DNS. Además, garantiza que las solicitudes y respuestas de DNS no sean manipuladas o falsificadas mediante [ataques en ruta](https://www.cloudflare.com/learning/security/threats/on-path-attack/).

[^2]: El DNS sobre HTTPS, o DoH, es una alternativa a DoT. Con DoH, las consultas y respuestas de DNS están encriptadas, pero se envían a través de los protocolos [HTTP](https://www.cloudflare.com/learning/ddos/glossary/hypertext-transfer-protocol-http/) o [HTTP/2](https://www.cloudflare.com/learning/performance/http2-vs-http1.1/), en lugar de hacerlo directamente por UDP. Al igual que DoT, DoH garantiza que los atacantes no puedan falsificar o alterar el tráfico de DNS. El tráfico DoH se parece al resto del tráfico HTTPS (por ejemplo, las interacciones normales de los usuarios con sitios y aplicaciones web) desde la perspectiva de un administrador de red.
