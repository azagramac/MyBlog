# Ejecutar tareas crontab

Podemos indicarle que hacer y cuando por medio de cron en el router, necesitamos que tenga Firmware merlin, tener activado la ejecución de scripts en la carpeta `/jffs/scripts` y un usb conectado al router.&#x20;



Para crear un cron, la sintaxis es la siguiente, importante! este cron se borrara en cada reinicio del router

```sh
cru a NOMBRE_DE_LA_TAREA "programacion comandos"
```

\
Listar los cron que tenemos corriendo.

```sh
r00t@RT-AX88U_Pro:/# cru l
11 14 */7 * * service restart_letsencrypt #LetsEncrypt#
*/2 * * * * /etc/openvpn/server1/vpn-watchdog1.sh #CheckVPNServer1#
0 */12 * * * /bin/sync && /bin/echo 3 > /proc/sys/vm/drop_caches #freeram#
0 */2 * * * /jffs/scripts/status.sh #telegram#
25 17 * * * sh /jffs/scripts/firewall banmalware #Skynet_banmalware#
21 1 * * Mon sh /jffs/scripts/firewall update #Skynet_autoupdate#
0 * * * * sh /jffs/scripts/firewall save #Skynet_save#
34 */12 * * * sh /jffs/scripts/firewall debug genstats #Skynet_genstats#
r00t@RT-AX88U_Pro:/#
```



Si queremos que sea persistente, ya que en cada reinicio se borran, debemos crear un script y guárdalo con permisos de ejecución en `/jffs/scripts`



Mi script en `/jffs/scripts/post-mount.sh`

```sh
#!/bin/sh
echo "Mount file swap..."
swapon /tmp/mnt/sda1/file.swp

echo "Add Free RAM to cron..."
cru a freeram "0 */12 * * * /bin/sync && /bin/echo 3 > /proc/sys/vm/drop_caches"

echo "Add Telegram to cron..."
cru a telegram "0 */2 * * * sh /jffs/scripts/status.sh"
```
