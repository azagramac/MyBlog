# Monitorizar temperaturas con Telegram

Con este sencillo script, no necesitamos tener firmware Merlin en el Asus, basta con habilitar el acceso SSH (recomiendo cambiar el puerto, por defecto es el 22), y un equipo con Linux 🐧 que tengamos 24/7, basta con una raspberryPi 🍓 o similar, en mi caso uso una [NanoPi Neo3](https://wiki.friendlyelec.com/wiki/index.php/NanoPi\_NEO3) 🐍 que tengo como servidor docker/rsyslog



El resultado seria como este, un mensaje cada 2h, lo podemos cambiar, ya que pondremos en un cron.&#x20;

<figure><img src="../.gitbook/assets/Captura desde 2023-07-07 08-14-19.png" alt=""><figcaption></figcaption></figure>

Lo primero, habilitar el acceso SSH en el router, para ello entramos en nuestro router via web

Entramos en Administration / System, y nos vamos al final de la pagina, en Service, aqui habilitamos el SSH, LAN Only, (ya que accederemos desde local, si vas hacerlo desde fuera, mejor por VPN), definimos un puerto, y copiamos nuestra clave ssh del servidor que vamos a usar, y guardamos los cambios.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



Ahora creamos un fichero de nombre `telegram.env`, y lo guardaremos donde queramos, este fichero contiene el token y chatID de nuestro Telegram, si no sabes cuales son tu token y chatID, los puedes obtener de estas cuentas de telegram:\


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

<pre class="language-sh"><code class="lang-sh">#!/bin/bash

<strong>unset $MODEL_NAME
</strong>unset $FIRMWARE_VERSION
unset $BANNER
unset $TEMP_CPU
unset $TEMP_WIFI2
unset $TEMP_WIFI5
unset $RAM_USED
unset $RAM_USED_PERCENTAGE
unset $SIGN_DATE
unset $CPU_TEMP
unset $UPTIME
unset $LIVE

/bin/ping -c2 "1.1.1.1" > /dev/null 2>&#x26;1
if [ $? -ne 0 ]
then
        exit 0
else
    HOST="IP ROUTER"
    PORT="POR SSH"
    USER="USER RPUTER"
    
    MODEL_NAME=$(ssh $USER@$HOST -p $PORT nvram get lan_hostname)
    FIRMWARE_VERSION=$(ssh $USER@$HOST -p $PORT nvram get innerver)
    RAM_USED=$(ssh $USER@$HOST -p $PORT free | grep Mem | awk '{ printf("%.1f", $3 / 1024) }')
    RAM_USED_PERCENTAGE=$(ssh $USER@$HOST -p $PORT free | grep Mem | awk '{ printf("%.2f", $3/$2 * 100.0) }')
    TEMP_CPU=$(ssh $USER@$HOST -p $PORT cat /sys/class/thermal/thermal_zone0/temp | awk '{printf("%.1f\n", $1 / 1000) }')
    CPU_TEMP=$(ssh $USER@$HOST -p $PORT cat /sys/class/thermal/thermal_zone0/temp | awk '{printf("%.0f\n", $1 / 1000) }')
    TEMP_WIFI2=$(ssh $USER@$HOST -p $PORT wl -i eth6 phy_tempsense | awk '{print $1 / 2 + 20}')
    TEMP_WIFI5=$(ssh $USER@$HOST -p $PORT wl -i eth7 phy_tempsense | awk '{print $1 / 2 + 20}')
    SIGN_DATE=$(ssh $USER@$HOST -p $PORT nvram get bwdpi_sig_ver)
    DAYS=$(ssh $USER@$HOST -p $PORT nvram get sys_uptime_now)
    UPTIME=$(date -ud "@$DAYS" +"$(( $DAYS/3600/24 )) days %H hours %M minutes %S seconds")
    
    ## free RAM
    ssh $USER@$HOST -p $PORT /bin/sync &#x26;&#x26; /bin/echo 3 > /proc/sys/vm/drop_caches
    
    TELEGRAM_AUTH="/root/telegram.env"
    TOKEN=$(cat $TELEGRAM_AUTH | grep "TOKEN" | awk -F "=" '{print $2}')
    CHATID=$(cat $TELEGRAM_AUTH | grep "CHAT_ID" | awk -F "=" '{print $2}')
    API_TELEGRAM="https://api.telegram.org/bot$TOKEN/sendMessage?parse_mode=HTML"
    
    LIMIT_TEMP_CPU=70

    function sendMessage()
    {
        curl -s -X POST $API_TELEGRAM \
            -d chat_id=$CHATID \
            -d text="$(printf "&#x3C;b>$BANNER&#x3C;/b>\n\n \
        CPU: $TEMP_CPUº\n \
        WiFi 2.4Ghz: $TEMP_WIFI2º\n \
        WiFi 5Ghz: $TEMP_WIFI5º\n \
        RAM Used: $RAM_USED_PERCENTAGE%%, $RAM_USED Mb\n \
        Firmware: $FIRMWARE_VERSION\n \
        Uptime: $UPTIME\n \
        Trend Micro sign: $SIGN_DATE\n")" > /dev/null 2>&#x26;1
    }
    
    if [ "$CPU_TEMP" -gt $LIMIT_TEMP_CPU ]
    then
        BANNER="❌ $MODEL_NAME 🔥"
        sendMessage
    else
        BANNER="✅ $MODEL_NAME ❄️"
        sendMessage
    fi
fi
</code></pre>

Configuramos el cron para que lo ejecute cuando deseemos, en mi caso cada 2h.

```sh
0 */2 * * * /root/script.sh
```

Y ya lo tendríamos.&#x20;
