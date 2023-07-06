# Servidor RSYSLOG

Montar un servidor de logs con rsyslog

<figure><img src="../.gitbook/assets/image (2) (1) (2).png" alt=""><figcaption></figcaption></figure>



Editamos el fichero `/etc/rsyslog.conf` en el servidor y descomentamos estas líneas, o solo la del protocolo que queramos usar, TCP o UDP.&#x20;

```bash
# provides UDP syslog reception
module(load="imudp")
input(type="imudp" port="514")

# provides TCP syslog reception
module(load="imtcp")
input(type="imtcp" port="514")
```

Ahora añadimos esta linea, definiendo la ruta donde queremos ver los logs de los clientes.

```bash
## Client logs files
$template RemoteLogs,"/root/rsyslog/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs
```

Guardamos los cambios y reiniciamos el servicio rsyslog

```sh
systemctl restart rsyslog
```

Comprobamos si se ha reiniciado correctamente

```sh
systemctl status rsyslog

● rsyslog.service - System Logging Service
     Loaded: loaded (/lib/systemd/system/rsyslog.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2023-05-26 13:14:37 CEST; 10s ago
TriggeredBy: ● syslog.socket
       Docs: man:rsyslogd(8)
             man:rsyslog.conf(5)
             https://www.rsyslog.com/doc/
   Main PID: 65551 (rsyslogd)
      Tasks: 10 (limit: 2204)
     Memory: 1.1M
        CPU: 27ms
     CGroup: /system.slice/rsyslog.service
             └─65551 /usr/sbin/rsyslogd -n -iNONE

May 26 13:14:37 RasPi systemd[1]: Starting System Logging Service...
May 26 13:14:37 RasPi rsyslogd[65551]: imuxsock: Acquired UNIX socket '/run/systemd/journal/syslog' (fd 3) from systemd.  [
v8.2102.0]
May 26 13:14:37 RasPi rsyslogd[65551]: [origin software="rsyslogd" swVersion="8.2102.0" x-pid="65551" x-info="https://www.r
syslog.com"] start
May 26 13:14:37 RasPi systemd[1]: Started System Logging Service.
```

Ahora configuramos los clientes, añadimos esta linea poniendo la IP del servidor donde tenemos el rsyslog y reiniciamos el servicio.&#x20;

```sh
*.*  @IP_SERVER_RSYSLOG:514
```

Ahora podremos ver en la ruta definida como los clientes empiezan a mandar los logs, ordenados por cliente y servicios

```bash
root@RasPi:~/rsyslog# ll
total 12
drwx------ 2 root root 4096 May 26 12:36 nanoPiDNS
drwx------ 2 root root 4096 May 25 14:17 RT-AX88U_Pro
drwx------ 2 root root 4096 May 26 01:06 antminer_l3+
root@RasPi:~/rsyslog# ls -l RT-AX88U_Pro/
total 460
-rw-r--r-- 1 root root    74 May 25 14:16  BONDING.log
-rw-r--r-- 1 root root   228 May 25 14:16  BWDPI.log
-rw-r--r-- 1 root root   237 May 25 14:16  FTP_Server.log
-rw-r--r-- 1 root root    92 May 25 14:16 "Let's_Encrypt.log"
-rw-r--r-- 1 root root   133 May 25 14:16  Mastiff.log
-rw-r--r-- 1 root root   158 May 25 14:16  RT-AX88U_Pro.log
-rw-r--r-- 1 root root   668 May 25 14:17  Samba_Server.log
-rw-r--r-- 1 root root   240 May 25 14:16  Timemachine.log
-rw-r--r-- 1 root root   182 May 25 14:16  WAN_Connection.log
-rw-r--r-- 1 root root   164 May 25 14:15  WEBDAV_Server.log
-rw-r--r-- 1 root root 11883 May 26 13:07  acsd.log
-rw-r--r-- 1 root root  5765 May 25 14:16  avahi-daemon.log
-rw-r--r-- 1 root root   180 May 25 14:17  awsiot.log
-rw-r--r-- 1 root root   361 May 25 14:16  cfg_server.log
-rw-r--r-- 1 root root   105 May 25 14:17  crond.log
-rw-r--r-- 1 root root   260 May 25 14:16  ddns.log
-rw-r--r-- 1 root root   282 May 25 14:16  disk_monitor.log
-rw-r--r-- 1 root root 91743 May 26 12:58  dnsmasq-dhcp.log
-rw-r--r-- 1 root root   161 May 25 14:16  dnsmasq-script.log
-rw-r--r-- 1 root root 12840 May 26 01:18  dnsmasq.log
-rw-r--r-- 1 root root   529 May 26 01:47  dropbear.log
-rw-r--r-- 1 root root    93 May 25 14:15  haveged.log
-rw-r--r-- 1 root root 85114 May 26 13:16  hostapd.log
-rw-r--r-- 1 root root   112 May 25 14:16  hotplug.log
-rw-r--r-- 1 root root    82 May 25 14:16  hour_monitor.log
-rw-r--r-- 1 root root   188 May 25 14:16  httpd.log
-rw-r--r-- 1 root root   225 May 25 14:16  iTunes.log
-rw-r--r-- 1 root root   353 May 25 14:16  inadyn.log
-rw-r--r-- 1 root root   122 May 25 14:16  init.log
-rw-r--r-- 1 root root  6295 May 25 17:41  kernel.log
-rw-r--r-- 1 root root    95 May 25 14:16  lldpcli.log
-rw-r--r-- 1 root root  1580 May 25 14:16  lldpd.log
-rw-r--r-- 1 root root   972 May 25 14:16  networkmap.log
-rw-r--r-- 1 root root   209 May 25 14:16  ntpd.log
-rw-r--r-- 1 root root 17049 May 26 02:32  ovpn-server1.log
-rw-r--r-- 1 root root  1866 May 25 14:16  pppd.log
-rw-r--r-- 1 root root  1863 May 26 01:18  rc_service.log
-rw-r--r-- 1 root root    75 May 25 14:16  roamast.log
-rw-r--r-- 1 root root  1002 May 26 01:18  stubby.log
-rw-r--r-- 1 root root   103 May 25 14:16  usb.log
-rw-r--r-- 1 root root    81 May 25 14:16  wan.log
-rw-r--r-- 1 root root 58834 May 26 12:35  wlceventd.log
-rw-r--r-- 1 root root   906 May 25 14:17  wsdd2.log
-rw-r--r-- 1 root root    87 May 25 14:17  zcip_client.log
```
