# Instalar Skynet

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Requisitos**

* Router Asus con Firmware Merlin ([ver dispositivos compatibles](https://github.com/RMerl/asuswrt-merlin.ng/wiki/Supported-Devices))
* USB conectado con al menos 4Gb libres en formato Ext4 (recomendable 8Gb, 2Gb para swap)
* [Swap habilitada](habilitar-swap.md)\


<figure><img src="../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>

**Instalación**

Descargar y asignar permisos

```sh
curl -s https://raw.githubusercontent.com/Adamm00/IPSet_ASUS/master/firewall.sh -o /jffs/scripts/firewall && chmod a+x /jffs/scripts/firewall
```

Instalación

```sh
/jffs/scripts/firewall install
```

Habilitamos el registro

<pre class="language-sh"><code class="lang-sh"><strong>/jffs/scripts/firewall settings logmode enable
</strong></code></pre>

Añadimos las listas a la blacklist, gracias [Juan](https://github.com/JuanRodenas) 😊

<pre class="language-sh"><code class="lang-sh"><strong>/jffs/scripts/firewall import blacklist https://raw.githubusercontent.com/JuanRodenas/Ubiquiti/main/list/ruzone.raw "Lista Rusia"
</strong></code></pre>

```sh
/jffs/scripts/firewall import blacklist https://raw.githubusercontent.com/JuanRodenas/Ubiquiti/main/list/cnzone.raw "Lista China"
```

```sh
/jffs/scripts/firewall import blacklist https://raw.githubusercontent.com/JuanRodenas/Ubiquiti/main/list/secureip.raw "Lista IPs Seguras"
```

Y reiniciamos el router.

Despues del reinicio, podemos comprobar su estado desde la WebUI del router.

<figure><img src="../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

Link skynet: [https://github.com/Adamm00/IPSet\_ASUS](https://github.com/Adamm00/IPSet\_ASUS)

Link firmware merlin: [https://www.asuswrt-merlin.net/](https://www.asuswrt-merlin.net/)

Link listas: [https://github.com/JuanRodenas/Ubiquiti/tree/main/list](https://github.com/JuanRodenas/Ubiquiti/tree/main/list)



Testado en Router RT-AX88U Pro, firmware Merlin v388.3\_0, SkyNet v7.4.4





**Comandos disponibles**

Unban Commands

```sh
firewall unban ip 8.8.8.8 # This Unbans The IP Specified
firewall unban range 8.8.8.8/24 # This Unbans the CIDR Block Specified
firewall unban domain google.com # This Unbans the URL Specified
firewall unban comment "Apples" # This Unbans Entries With The Comment Apples
firewall unban country # This Unbans Entries Added By The "Ban Country" Feature
firewall unban asn AS123456 # This Unbans the ASN Specified
firewall unban malware # This Unbans Entries Added By The "Ban Malware" Feature
firewall unban nomanual # This Unbans Everything But Manual Bans
firewall unban all # This Unbans All Entries From Both Blacklists
```



Ban Commands

```
firewall ban ip 8.8.8.8 "Apples" # This Bans The IP Specified With The Comment Apples
firewall ban range 8.8.8.8/24 "Apples" # This Bans the CIDR Block Specified With The Comment Apples
firewall ban domain google.com # This Bans the URL Specified
firewall ban country "pk cn sa" # This Bans The Known IPs For The Specified Countries (Accepts Single/Multiple Inputs If Quoted) https://www.ipdeny.com/ipblocks/
firewall ban asn AS123456 # This Bans the ASN Specified
```



Banmalware Commands

```sh
firewall banmalware # This Bans IPs From The Predefined Filter List
firewall banmalware google.com/filter.list # This Uses The Filter List From The Specified URL
firewall banmalware reset # This Will Reset Skynet Back To The Default Filter URL
firewall banmalware exclude "list1.ipset|list2.ipset" # This Will Exclude Lists Matching The Names "list1.ipset list2.ipset" From The Current Filter (Quotes And Pipes Are Nessessary For Seperating Multiple Entries!)
firewall banmalware exclude reset # This Will Reset The Exclusion List
```



Whitelist Commands

```sh
firewall whitelist ip 8.8.8.8 "Apples" # This Whitelists The IP Specified With The Comment Apples
firewall whitelist range 8.8.8.8/24 "Apples" # This Whitelists The Range Specified With The Comment Apples
firewall whitelist domain google.com) This Whitelists the URL Specified
firewall whitelist asn AS123456 # This Whitelists the ASN Specified
firewall whitelist vpn) Refresh VPN Whitelist
firewall whitelist remove all) This Removes All Non-Default Entries
firewall whitelist remove entry 8.8.8.8) This Removes IP/Range Specified
firewall whitelist remove comment "Apples" # This Removes Entries With The Comment Apples
firewall whitelist refresh # Regenerate Shared Whitelist Files
firewall whitelist view ips|domains|imported # View Whitelist Entries Based On Category (Leave Blank For All)
```



Import list

```sh
firewall import blacklist URL_LIST # This Bans All IPs From URL/Local File With The Comment Apples
firewall import whitelist URL_LIST # This Whitelists All IPs From URL/Local File With The Comment Apples
```



Deport list

```sh
firewall deport blacklist URL_LIST # This Unbans All IPs From URL/Local File
firewall deport whitelist URL_LIST # This Unwhitelists All IPs From URL/Local File
```



Update Commands

```sh
firewall update # Standard Update Check - If Nothing Detected Exit
firewall update check # Check For Updates Only - Wont Update If Detected
firewall update -f # Force Update Even If No Changes Detected
```



Settings Commands

```sh
firewall settings autoupdate enable|disable # Enable/Disable Skynet Autoupdating
firewall settings banmalware daily|weekly|disable # Enable/Disable Automatic Malware List Updating
firewall settings logmode enable|disable # Enable/Disable Logging
firewall settings filter all|inbound|outbound # Select What Traffic To Filter
firewall settings unbanprivate enable|disable # Enable/Disable Unban_PrivateIP Function
firewall settings loginvalid enable|disable # Enable/Disable Invalid Packet Logging
firewall settings banaiprotect enable|disable # Enable/Disable Banning IPs Flagged By AiProtect
firewall settings securemode enable|disable # Enable/Disable Insecure Settings Being Applied In WebUI
firewall settings fs google.com/filter.list|disable # Configure/Disable Fast Malware List Switching
firewall settings syslog|syslog1 /tmp/syslog.log|default # Configure Custom Syslog/Syslog-1 Location
firewall settings iot unban|ban 8.8.8.8,9.9.9.9 # Unban|Ban IOT Device(s) (or CIDR) From Accessing WAN (Allow NTP / Remote Access Via OpenVPN Only) (Use Comma As Separator)
firewall settings iot view # View Currently Banned IOT Devices
firewall settings iot ports 123,124,125 # Allow Port(s) To Access WAN (Use Comma As Separator)
firewall settings iot ports reset # Reset Allowed Port List To Default
firewall settings iot proto udp|tcp|all # Select IOT Allowed Port Protocol
firewall settings lookupcountry enable|disable # Enable/Disable Country Lookup For Stat Data
firewall settings cdnwhitelist enable|disable # Enable/Disable CDN Whitelisting
firewall settings webui enable|disable # Enable/Disable WebUI
```



Debug Commands

```sh
firewall debug watch # Show Debug Entries As They Appear
firewall debug info # Print Useful Debug Info
firewall debug info extended # Debug Info + Config
firewall debug genstats # Update WebUI Stats
firewall debug clean # Cleanup Syslog Entries
firewall debug swap install|uninstall # Install/Uninstall SWAP File
firewall debug backup # Backup Skynet Files To Skynets Install Directory With The Name "Skynet-Backup.tar.gz"
firewall debug restore # Restore Backup Files From Skynets Install Directory With The Name "Skynet-Backup.tar.gz"
```



Stats Commands

```sh
firewall stats # Compile Stats With Default Top10 Output
firewall stats 20 # Compile Stats With Customizable Top20 Output
firewall stats tcp # Compile Stats Showing Only TCP Entries
firewall stats tcp 20 # Compile Stats Showing Only TCP Entries With Customizable Top20 Output
firewall stats search port 23 # Search Logs For Entries On Port 23
firewall stats search port 23 20 # Search Logs For Entries On Port 23 With Customizable Top20 Output
firewall stats search ip 8.8.8.8 # Search Logs For Entries On 8.8.8.8
firewall stats search ip 8.8.8.8 20 # Search Logs For Entries On 8.8.8.8 With Customizable Top20 Output
firewall stats search malware 8.8.8.8 # Search Malwarelists For Specified IP
firewall stats search manualbans # Search For All Manual Bans
firewall stats search device 192.168.1.134 # Search For All Outbound Entries From Local Device 192.168.1.134
firewall stats search device reports # Search Previous Hourly Report History
firewall stats search invalid # Search For Invalid Packets
firewall stats search iot # Search For IOT Packets
firewall stats search connections ip|port|proto|id xxxxxxxxxx) Search Active Connections
firewall stats remove ip 8.8.8.8 # Remove Log Entries Containing IP 8.8.8.8
firewall stats remove port 23 # Remove Log Entries Containing Port 23
firewall stats reset # Reset All Collected Logs
```
