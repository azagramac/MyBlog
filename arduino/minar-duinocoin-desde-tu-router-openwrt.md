# Minar DuinoCoin desde tu router OpenWRT

Si, es correcto el titular del post, minar con tu router es posible.\
Logicamente no es cualquier moneda muy conocida, como Bitcoin o Ethereum, para estas necesitas otro tipo de hardware, ASIC para bitcoin, y GPU/y algun asic concreto para Ethereum.\
\
[Duinocoin](https://duinocoin.com), una moneda que es posible minar con una placa Arduino, ESP32, ESP8266, RaspberryPi, PC, o cualquier cosa que puedas ejecutar Python 3, basicamente, utiliza el algoritmo SHA-1.

Para empezar lo que necesitamos es, un router, no vale cualquiera... el HGU de Movistar/O2, no vale, necesitas uno que puedas cargar [OpenWRT](https://openwrt.org), o que tenga paqueteria opkg, como los Asus con [firmware Merlin](https://www.asuswrt-merlin.net), en mi caso, tengo un [RT-AX58U](https://www.asus.com/es/Networking-IoT-Servers/WiFi-Routers/ASUS-WiFi-Routers/RT-AX58U/) de Asus, y la ultima version del firmware merlin actual, la v[386.3\_4](https://onedrive.live.com/?authkey=%21AJLLKAY%2D%2D4EBqDo\&id=CCE5625ED3599CE0%211440\&cid=CCE5625ED3599CE0)

![](../.gitbook/assets/img\_ruouterDuino.png)

Lo primero es tener instalado el firmware-merlin, este paso me lo salto, si ya lo tienes instalado, seria tan facil como instalar via SSH en el router los siguientes paquetes.\
\
Lo primero es tener el gestor de paquete opkg, si no lo tienes instalalo asi en el router, necesitaras un pendrive formateado en Ext4 previamente.

```
wget -c https://raw.githubusercontent.com/AzagraMac/MineCryptoOnWifiRouter/main/entware-ngu-setup.sh
```

Una vez instalado, reinicia el router, apagandolo completamente desde el boton.\
Ahora ya solo queda, entrar en el router y ejecutar:\
\
Primero actualizamos los repositorios

```
opkg update
```

y ahora instalamos los paquetes

```
opkg install python3
opkg install coreutils-nohup
```

y reiniciamos de nuevo el router, ya lo tendriamos.

Ahora descargamos el script, y editamos la variable "**username**" para añadir nuestro usuario de DuinoCoin

```
wget -c https://raw.githubusercontent.com/AzagraMac/MineCryptoOnWifiRouter/main/miner.py
```

y listo, para lanzarlo

```
python3 miner.sh
```

Si lo quieres lanzar en segundo plano, para no tener la consola abierta en todo momento...

```
nohup python3 -u miner.py > /tmp/mnt/sda1/miner.log 2>&1 &
```

En este caso, le decimos que la salidad standar nos la saque a un fichero .log para poder consultarlo mas adelante, y el proceso pase a segundo plano.\
\
Y a minar

![](../.gitbook/assets/img\_ruouterDuino2.png)

Si consultamos el fichero de log, vemos el estado y los shares.

```
root@RT-AX58U:/jffs# tail -f /tmp/mnt/sda1/miner.log 
Accepted share 455023 Hashrate 41 kH/s Difficulty 13026
Accepted share 639228 Hashrate 42 kH/s Difficulty 7500
Accepted share 13308 Hashrate 42 kH/s Difficulty 5485
Accepted share 1452460 Hashrate 41 kH/s Difficulty 14788
Accepted share 233854 Hashrate 41 kH/s Difficulty 2500
Accepted share 163341 Hashrate 42 kH/s Difficulty 11479
Accepted share 108663 Hashrate 41 kH/s Difficulty 12562
Accepted share 617008 Hashrate 41 kH/s Difficulty 13348
Accepted share 472074 Hashrate 41 kH/s Difficulty 5687
Accepted share 171341 Hashrate 41 kH/s Difficulty 7500
```

Si deseas, puedes hacer una donacion de DUCO **ᕲ** :coffee: a **`azagramac`** :sweat\_smile:&#x20;
