---
cover: ../.gitbook/assets/cover.jpg
coverY: 0
---

# Instalar HDD-OSD

**Requisitos Hardware:**

* PlayStation 2 (fat)
* MemoryCard
* Adaptador de Red oficial SCPH-10350 / SCPH-10281 (_compatible con adaptadores de red de no oficiales_)
* Disco Duro IDE si no tienes el mod de SATA, recomendable el de BitFunx
* Disco Duro SATA, válidos algunos SSD, max. 2Tb



**Requisitos Software:**

* [HDD-OSD 1.00J](https://archive.org/details/hddosd-100j-update-110u-48bit-atadp)
* [wLaunchELF ISR](https://github.com/israpps/wLaunchELF_ISR/releases)
* [Open PS2 Loader](https://github.com/ps2homebrew/Open-PS2-Loader/releases)



Mi hardware es:

* PlayStation 2 v7 SCPH-39004 (también una v9 SCPH-50004)
* Adaptador de red oficial de Sony, SCPH-10350\
  ![](<../.gitbook/assets/image (190).png>)![](<../.gitbook/assets/image (191).png>)
* [Adaptador SATA de BitFunx](https://es.aliexpress.com/item/33060033230.html)\
  <img src="../.gitbook/assets/image (192).png" alt="" data-size="original">
* HDD [Western Digital WD5000AZLX](https://www.amazon.es/dp/B00E1C93L0) de 500Gb 3.5", mas que de sobra, a no ser que quieras tener casi todo el FullSet.\
  ![](<../.gitbook/assets/image (194).png>)<br>

\
Tenemos 2 .img RAW para grabar en el HDD:

`HDDOSD_100J_ATADP_DTL-H_FIX_48BIT_APPS.IMG` - md5: `83e5f2b624b18d28c2c912663c48c370`\
`HDDOSD_100J_ATADP_DTL-H_FIX_48BIT.IMG` - md5: `952410e5a31b9ceb3a998b2d59735cfb`\
\
con apps que incluye unas versiones algo obsoletas de uLaunchELF y OPL, o sin apps, limpio completamente, solo el hdd-osd formateado, sin aplicaciones, me gusta más esta segunda opción, para dejarlo a tu gusto, requiere de un método como [FreeMCBoot](freemcboot.md) para cargar el uLaunchELF inicialmente y crear las particiones:&#x20;

* `PP.ULE` Partición para el uLaunchELF, config y ejecutable .kelf, tamaño 128Mb.
* `PP.OPL` Partición para el OPL, config y ejecutable .kelf, tamaño 128Mb.
* `+OPL` la partición "+OPL" la crea el OPL la primera vez que se inicia, en caso contrario, crearla a mano, max 2Gb.
* `__.POPS` Partición para los juegos de PSX en formato .VCD

{% hint style="info" %}
El orden es importante para que el OPL cargue más rápido, de lo contrario tardará más en cargarse si hay juegos de PS2 que se instalaron antes de crear la partición `+OPL`.\
\
Asimismo la partición `__.POPS` para el emulador de PSX debe crearse justo después de haber creado la partición `+OPL`, y por último empezar a instalar los juegos de PS2 con HDL-Batch-installer.
{% endhint %}

\
Conectamos nuestro HDD o SSD al ordenador, bien por cable SATA o USB en una caja (IDE/SATA), tenemos que conocer la letra del dispositivo, `/dev/sdb` por ejemplo.&#x20;

```bash
sudo dd if=HDDOSD_100J_ATADP_DTL-H_FIX_48BIT.IMG of=/dev/sdb bs=1M status=progress conv=fsync
```

\
Una vez terminado, ya hemos acabado de momento con el HDD, lo conectamos al adaptador de red de la PS2 y lo metemos en la consola, asegúrate que queda bien conectado, no es necesario atornillar aun los 2 tornillos, además se pasan enseguida las roscas del lado de la consola, para restaurar la rosca tienes que desmontar la consola, quitar el chasis...&#x20;



En construcción...&#x20;
