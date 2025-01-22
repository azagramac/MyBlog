---
description: >-
  Parámetros de configuración de mi red Home con varias VLANs, 🚧(en
  construcción)🚧
---

# Configuración vía terminal, red Home

Hardware:

* EdgeRouter 4, firmware v2.0.9-hotfix.7
* EdgeSwitch 8-150, firmware 1.10.4-lite

<figure><img src="../.gitbook/assets/imagen (39).png" alt=""><figcaption></figcaption></figure>

![](<../.gitbook/assets/imagen (43).png>)  ![](<../.gitbook/assets/imagen (44).png>)



Parámetros vía consola en **EdgeRouter**:

\
**UISP**

```sh
configure
set service unms connection 'wss://{your_domain}.uisp.com:443+{token}+allowSelfSignedCertificate'
commit ;save
exit
```



**DDNS con Cloudflare**

```sh
configure
set service dns dynamic interface pppoe0 service custom-cloudflare protocol cloudflare
set service dns dynamic interface pppoe0 service custom-cloudflare server api.cloudflare.com/client/v4
set service dns dynamic interface pppoe0 service custom-cloudflare host-name {SUB.YOUR_DOMAIN.com}
set service dns dynamic interface pppoe0 service custom-cloudflare login "{YOUR_MAIL_ACCOUNT_CLOUDFLARE}"
set service dns dynamic interface pppoe0 service custom-cloudflare password "{GLOBAL KEY}"
set service dns dynamic interface pppoe0 service custom-cloudflare options "zone={YOUR_DOMAIN.com} use=web ssl=yes ttl=1"
commit ;save
exit
```



**Hardware Offloading**, [mas información](https://help.ui.com/hc/en-us/articles/115006567467-EdgeRouter-Hardware-Offloading).

```sh
configure
set system offload ipv4 forwarding enable
set system offload ipv4 pppoe enable
set system offload ipv4 vlan enable
set system offload ipv6 forwarding enable
set system offload ipv6 pppoe enable
set system offload ipv6 vlan enable
set system offload ipsec enable
commit ;save
exit
```



**LLDP**

```sh
configure
set service lldp interface all
commit ;save
exit
```



**Delete traffic-control**

```sh
configure
delete traffic-control
delete system flow-accounting
commit ;save
exit
```



**MAC del router del operador de fibra**, cambiar el dispositivo eth0 por el que corresponda a la WAN

```sh
configure
set interfaces ethernet eth0 mac {MAC_ROUTER_ISP}
commit ;save
exit
```



**Rules RFC 1918**

```sh
configure
set firewall group network-group RFC1918-Ranges description 'RFC 1918 Ranges'
set firewall group network-group RFC1918-Ranges network 192.168.0.0/16
set firewall group network-group RFC1918-Ranges network 172.16.0.0/12
set firewall group network-group RFC1918-Ranges network 10.0.0.0/8
commit ;save
exit
```



**Otros parámetros**

```sh
set service ssh port 22
set service ssh protocol-version v2
set system name-server 1.1.1.1
set system name-server 1.0.0.1
set system ntp server time.google.com
set system ntp server time.cloudflare.com
set system ntp server 0.ubnt.pool.ntp.org
set system ntp server 3.ubnt.pool.ntp.org
```



**Configuración de los interfaces**

```sh
set interfaces ethernet eth1 address 192.168.1.1/24
set interfaces ethernet eth1 description eth1
set interfaces ethernet eth1 duplex auto
set interfaces ethernet eth1 poe output off
set interfaces ethernet eth1 speed auto

set interfaces ethernet eth2 address 172.16.1.1/24
set interfaces ethernet eth2 description eth2
set interfaces ethernet eth2 duplex auto
set interfaces ethernet eth2 poe output off
set interfaces ethernet eth2 speed auto

set interfaces ethernet eth3 address 10.1.1.1/24
set interfaces ethernet eth3 description eth3
set interfaces ethernet eth3 duplex auto
set interfaces ethernet eth3 speed auto
```



Parámetros vía consola en **EdgeSwitch**:

**UISP**

```sh
enable
configuration
service unms
service unms key wss://{your_domain}.uisp.com:443+{token}+allowSelfSignedCertificate
exit
write memory confirm
exit
```



**LLDP**

```sh
enable
configuration
interface 0/1-0/10
lldp transmit
lldp receive
lldp transmit-tlv port-desc
lldp transmit-tlv sys-name
lldp transmit-tlv sys-desc
lldp transmit-tlv sys-cap
lldp transmit-mgmt
lldp notification
lldp med
lldp med confignotification
exit
write memory
```
