# Deshabilitar el ahorro de energia en WiFi

Por defecto la RaspberryPi Zero W (y otros modelos), tiene el ahorro energia habilitado en el dispositivo wlan0, lo podemos cambiar, de no hacerlo, pasado un tiempo perdemos la conexion a ella...\
\
Podemos verlo con el siguiente comando si lo tenemos habilitado

```shell
$ sudo iwconfig wlan0
wlan0     IEEE 802.11  ESSID:"XXXXXXXXXXX"  
          Mode:Managed  Frequency:2.427 GHz  Access Point: XX:XX:XX:XX:XX:XX   
          Bit Rate=39 Mb/s   Tx-Power=31 dBm   
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:on
          Link Quality=48/70  Signal level=-62 dBm  
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:7  Invalid misc:0   Missed beacon:0
```

Lo podemos ver en Power Management:on, lo tenemos habilitado.\
Tambien podemos verlo desde el dmesg

```shell
$ dmesg | grep power
[    3.584372] bcm2835-power bcm2835-power: Broadcom BCM2835 power domains driver
[   30.281249] brcmfmac: brcmf_cfg80211_set_power_mgmt: power save enabled
```

Podemos hacer los cambios para la sesion actual, o tenerlo por defecto en el inicio.\
\
para la sesion actual, solo ejecutamos:

```shell
sudo iwconfig wlan0 power off
```

y verificamos que lo tenemos deshabilitado, ahora nos aparece en off, Power Management:off

```shell
$ sudo iwconfig wlan0
wlan0     IEEE 802.11  ESSID:"XXXXXXXXXXX"  
          Mode:Managed  Frequency:2.427 GHz  Access Point: XX:XX:XX:XX:XX:XX   
          Bit Rate=65 Mb/s   Tx-Power=31 dBm   
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:off
          Link Quality=53/70  Signal level=-57 dBm  
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:3  Invalid misc:0   Missed beacon:0
```

Y en el dmesg, vemos una nuea linea que nos indica que esta deshabilitado.

```shell
$ dmesg | grep power
[    3.584372] bcm2835-power bcm2835-power: Broadcom BCM2835 power domains driver
[   30.281249] brcmfmac: brcmf_cfg80211_set_power_mgmt: power save enabled
[   39.812210] brcmfmac: brcmf_cfg80211_set_power_mgmt: power save disabled
```

Pero si reiniciamos, volveremos a tenerlo habilitado, si lo queremos deshabilitado en el inicio...

Creamos un script y lo guardamos en la ruta que queramos, ejemplo en nuestro /home

```shell
#!/bin/sh -
iwconfig wlan0 power off
```

Guardamos el script, y le damos permisos de ejecución.

Ahora nos creamos un servicio en systemd, que sera el encargado de llamar al script en cada inicio del sistema.

```shell
sudo vim /etc/systemd/system/powersaveoff.service
```

```shell
[Unit]
Description=PowerSave disabled
After=multi-user.target
 
[Service]
Type=idle
ExecStart=/home/pi/powersaveOff.sh
 
[Install]
WantedBy=multi-user.target
```

Guardamos el fichero y lo habilitamos para que lo arranque en el inicio

```shell
sudo systemctl enable powersaveoff.service
```

Ahora podremos reiniciar, y comprobar que tenemos deshabilitado el ahorro de energia en el dispositivo wlan0

```shell
$ sudo iwconfig wlan0
wlan0     IEEE 802.11  ESSID:"XXXXXXXXXXX"  
          Mode:Managed  Frequency:2.427 GHz  Access Point: XX:XX:XX:XX:XX:XX   
          Bit Rate=77 Mb/s   Tx-Power=44 dBm   
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:off
          Link Quality=65/70  Signal level=-36 dBm  
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:3  Invalid misc:0   Missed beacon:0
```

Ready :thumbsup:
