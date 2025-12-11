# Monitorizar temperaturas con Telegram

Con este sencillo script, no necesitamos tener firmware Merlin en el Asus, basta con habilitar el acceso SSH (recomiendo cambiar el puerto, por defecto es el 22), y un equipo con Linux 🐧 que tengamos 24/7, basta con una raspberryPi 🍓 o similar, en mi caso uso una [NanoPi Neo3](https://wiki.friendlyelec.com/wiki/index.php/NanoPi_NEO3) 🐍 que tengo como servidor docker/rsyslog



El resultado seria como este, un mensaje cada 2h, lo podemos cambiar, ya que pondremos en un cron.&#x20;

<figure><img src="../.gitbook/assets/Captura desde 2023-07-07 08-14-19.png" alt=""><figcaption></figcaption></figure>

Lo primero, habilitar el acceso SSH en el router, para ello entramos en nuestro router via web

Entramos en Administration / System, y nos vamos al final de la pagina, en Service, aqui habilitamos el SSH, LAN Only, (ya que accederemos desde local, si vas hacerlo desde fuera, mejor por VPN), definimos un puerto, y copiamos nuestra clave ssh del servidor que vamos a usar, y guardamos los cambios.

<figure><img src="../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>



Ahora creamos un fichero de nombre `telegram.env`, y lo guardaremos donde queramos, este fichero contiene el token y chatID de nuestro Telegram, si no sabes cuales son tu token y chatID, los puedes obtener de estas cuentas de telegram:<br>

{% embed url="https://t.me/BotFather" %}
TOKEN
{% endembed %}

{% embed url="https://t.me/myidbot" %}
Chat ID
{% endembed %}

```sh
TOKEN=TU TOKEN
CHAT_ID=TU CHAT ID
```



El script, debes cambiar unos parámetros para adaptarlo a nuestra configuración

```sh
    HOST="IP ROUTER"
    PORT="POR SSH"
    USER="USER RPUTER"
    TELEGRAM_AUTH="/root/telegram.env"
```



Y guardamos el script donde queramos.&#x20;

```sh
#!/bin/bash

/bin/ping -c2 "1.1.1.1" > /dev/null 2>&1
if [ $? -ne 0 ]
then
    exit 1
else
    unset $MODEL_NAME
    unset $FIRMWARE_VERSION
    unset $BANNER
    unset $TEMP_CPU
    unset $TEMP_WIFI2
    unset $TEMP_WIFI5
    unset $RAM_USED
    unset $RAM_USED_PERCENTAGE
    unset $SWAP_USED
    unset $SWAP_FREE   
    unset $SIGN_DATE
    unset $CPU_TEMP
    unset $UPTIME
    unset $LIVE_TIME

    HOST="IP_ROUTER"
    PORT="PORT_SSH_ROUTER"
    USER="USER_ROUTER"
    
    MODEL_NAME=$(ssh $USER@$HOST -p $PORT nvram get lan_hostname)
    FIRMWARE_VERSION=$(ssh $USER@$HOST -p $PORT nvram get innerver)
    RAM_USED=$(ssh $USER@$HOST -p $PORT free | grep Mem | awk '{ printf("%.1f", $3 / 1024) }')
    RAM_FREE=$(ssh $USER@$HOST -p $PORT free | grep Mem | awk '{ printf("%.1f", $4 / 1024) }')
    SWAP_USED=$(ssh $USER@$HOST -p $PORT free | grep Swap | awk '{ printf("%.1f", $3 / 1024) }')
    SWAP_FREE=$(ssh $USER@$HOST -p $PORT free | grep Swap | awk '{ printf("%.1f", $4 / 1024) }')
    RAM_USED_PERCENTAGE=$(ssh $USER@$HOST -p $PORT free | grep Mem | awk '{ printf("%.2f", $3/$2 * 100.0) }')
    TEMP_CPU=$(ssh $USER@$HOST -p $PORT cat /sys/class/thermal/thermal_zone0/temp | awk '{printf("%.1f\n", $1 / 1000) }')
    CPU_TEMP=$(ssh $USER@$HOST -p $PORT cat /sys/class/thermal/thermal_zone0/temp | awk '{printf("%.0f\n", $1 / 1000) }')
    TEMP_WIFI2=$(ssh $USER@$HOST -p $PORT wl -i eth6 phy_tempsense | awk '{print $1 / 2 + 20}')
    TEMP_WIFI5=$(ssh $USER@$HOST -p $PORT wl -i eth7 phy_tempsense | awk '{print $1 / 2 + 20}')
    SIGN_DATE=$(ssh $USER@$HOST -p $PORT nvram get bwdpi_sig_ver)
    LIVE_TIME=$(ssh $USER@$HOST -p $PORT nvram get sys_uptime_now)
    UPTIME=$(date -ud "@$LIVE_TIME" +"$(( $LIVE_TIME/3600/24 )) days, %Hh %Mm")
    
    TELEGRAM_AUTH="/root/scripts/telegram.env"
    TOKEN=$(cat $TELEGRAM_AUTH | grep "TOKEN" | awk -F "=" '{print $2}')
    CHATID=$(cat $TELEGRAM_AUTH | grep "CHAT_ID" | awk -F "=" '{print $2}')
    API_TELEGRAM="https://api.telegram.org/bot$TOKEN/sendMessage?parse_mode=HTML"
    
    LIMIT_TEMP_CPU=70

    function sendMessage()
    {
        curl -s -X POST $API_TELEGRAM \
            -d chat_id=$CHATID \
            -d text="$(printf "<b>$BANNER</b>\n\n \
        🌡<b>Temperatures</b>\n \
        CPU: $TEMP_CPUº\n \
        WiFi 2.4Ghz: $TEMP_WIFI2º\n \
        WiFi 5Ghz: $TEMP_WIFI5º\n\n \
        📊 <b>Statistics</b>\n \
        RAM Free: $RAM_FREE\n \
        RAM Used: $RAM_USED Mb, $RAM_USED_PERCENTAGE%% \n \
        Swap Free: $SWAP_FREE / Used: $SWAP_USED Mb\n\n \
        📝 <b>Information</b>\n \
        Firmware: $FIRMWARE_VERSION\n \
        Uptime: $UPTIME\n \
        Trend Micro sign: $SIGN_DATE\n")" > /dev/null 2>&1
    }
    
    if [[ $CPU_TEMP -ge $LIMIT_TEMP_CPU ]]
    then
        BANNER="📡 $MODEL_NAME 🔥"
        sendMessage
    else
        BANNER="📡 $MODEL_NAME"
        sendMessage
    fi
fi
```

Configuramos el cron para que lo ejecute cuando deseemos, en mi caso cada 2h.

```sh
0 */2 * * * /root/script.sh
```



Para no saturar de accesos SSH, vamos a reutilizar o multiplexar la conexión SSH, para ello en el servidor que vamos a usar

```sh
mkdir ~/.ssh/sockets
```

Y creamos un fichero en `~/.ssh/config`

```sh
Host *
    ControlMaster auto
    ControlPath ~/.ssh/sockets/%r@%h:%p
    ControlPersist 600s
```

* `ControlMaster auto`: permite reutilizar una conexión SSH por múltiples sesiones. Por defecto está seteado a `no`. El parámetro `auto` permite al cliente SSH utilizar una conexión existente, y si no existe, crear una nueva (el caso de la primer sesión contra un servidor).\
  Esta opción requiere de otra opción: `ControlPath`, para funcionar.
* `ControlPath <ruta>`: esta opción permite especificar la ruta, dentro del sistema de archivos, al socket Unix que se utilizará para compartir la conexión entre varias sesiones.\
  En mi caso, el archivo será `~/.ssh/sockets/%r@%h:%p` (más adelante explico estos comodines/wildcards).
* `ControlPersist 600s`: esta opción debe ser usada junto con ControlMaster, y permite especificar el tiempo que la conexión principal permanecerá activa en segundo plano una vez que todas las sesiones contra un determinado servidor se hayan cerrado. Si la conexión está activa, cualquier nueva sesión la utilizará. Una vez que haya expirado el tiempo, la conexión se cerrará, se eliminará el archivo de socket Unix, y la siguiente sesión creará una nueva conexión.\
  Aquí he especificado 600 segundos, pero otras opciones válidas son `no` (para que se cierre la conexión al cerrar la última sesión) o `yes` (para mantener permanentemente la conexión abierta).

Los comodines o tokens válidos para el nombre de archivo son los siguientes (extraídos directamente desde `man ssh_config`):

<table data-header-hidden><thead><tr><th width="101"></th><th></th></tr></thead><tbody><tr><td><code>%%</code></td><td>Un caracter ‘%’</td></tr><tr><td><code>%C</code></td><td>Equivalente a <code>%l%h%p%r</code></td></tr><tr><td><code>%d</code></td><td>Directorio local del usuario.</td></tr><tr><td><code>%f</code></td><td>El fingerprint de la clave del servidor</td></tr><tr><td><code>%H</code></td><td>El nombre o dirección del host al que se conecta, extraído de <code>known_hosts</code>.</td></tr><tr><td><code>%h</code></td><td>El hostname del servidor remoto</td></tr><tr><td><code>%i</code></td><td>El UID del usuario local</td></tr><tr><td><code>%K</code></td><td>La clave del host codificada usando Base64</td></tr><tr><td><code>%k</code></td><td>Si está especificado, el alias de la clave del host, de lo contrario, el nombre de host remoto especificado en la línea de comando de conexión</td></tr><tr><td><code>%L</code></td><td>El hostname del equipo local</td></tr><tr><td><code>%l</code></td><td>El hostname del equipo local, incluyendo el dominio completo</td></tr><tr><td><code>%n</code></td><td>El hostname remoto original pasado por argumento en la línea de comando de conexión</td></tr><tr><td><code>%p</code></td><td>El puerto de la conexión remota</td></tr><tr><td><code>%r</code></td><td>El nombre de usuario remoto</td></tr><tr><td><code>%T</code></td><td>La interfaz local de tipo tun o tap asignada para el túnel SSH en el caso de que se haya solicitado reenvío de túnel. En caso contrario será <code>NONE</code>.</td></tr><tr><td><code>%t</code></td><td>El tipo de clave utilizada para el servidor remoto, por ejemplo, <em>ssh-ed25519</em>.</td></tr><tr><td><code>%u</code></td><td>El nombre de usuario local</td></tr></tbody></table>

En nuestro caso, el archivo especificado es: `%r@%h:%p`, o sea, `usuarioRemoto@hostRemoto:puerto`, lo que nos va a permitir identificar perfectamente qué archivo de socket corresponde con cada conexión remota.

\
Y ya lo tendríamos.&#x20;
