# Instalar Skynet

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Requisitos**

* Router Asus con Firmware Merlin ([ver dispositivos compatibles](https://github.com/RMerl/asuswrt-merlin.ng/wiki/Supported-Devices))
* USB conectado con al menos 4Gb libres en formato Ext4 (recomendable 8Gb, 2Gb para swap)
* Swap habilitada\


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

<pre class="language-sh"><code class="lang-sh"><strong>/jffs/scripts/firewall import blacklist https://raw.githubusercontent.com/JuanRodenas/Ubiquiti/main/list/ruzone.raw
</strong></code></pre>

```sh
/jffs/scripts/firewall import blacklist https://raw.githubusercontent.com/JuanRodenas/Ubiquiti/main/list/cnzone.raw
```

```sh
/jffs/scripts/firewall import blacklist https://raw.githubusercontent.com/JuanRodenas/Ubiquiti/main/list/secureip.raw
```

Y reiniciamos el router.

Despues del reinicio, podemos comprobar su estado desde la WebUI del router.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Link skynet: [https://github.com/Adamm00/IPSet\_ASUS](https://github.com/Adamm00/IPSet\_ASUS)

Link firmware merlin: [https://www.asuswrt-merlin.net/](https://www.asuswrt-merlin.net/)

Link listas: [https://github.com/JuanRodenas/Ubiquiti/tree/main/list](https://github.com/JuanRodenas/Ubiquiti/tree/main/list)



Testado en Router RT-AX88U Pro, firmware Merlin v388.3\_0, SkyNet v7.4.4
