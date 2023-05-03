# Error ata9 en dispositivo SATA

En kernel superior al 5.13, en ciertos dispositivos SATA como un SSD/HDD o una unidad óptica como un DVD o BluRay, nos da errores de este tipo:

```sh
[  176.012197] ata9: SATA link up 1.5 Gbps (SStatus 113 SControl 300)
[  176.030125] ata9.00: configured for UDMA/133
[  186.473202] ata9.00: limiting speed to UDMA/100:PIO4
[  187.352274] ata9: SATA link up 1.5 Gbps (SStatus 113 SControl 300)
[  187.386497] ata9.00: configured for UDMA/100
[  196.521329] ata9: SATA link up 1.5 Gbps (SStatus 113 SControl 300)
[  196.531085] ata9.00: configured for UDMA/100
```

\
Para ello o bien usamos un kernel 5.13 o inferior (el 5.4 y 5.8, van estupendamente bien), o tocamos un parámetro en el sistema.\
\
Lo primero sera consultar que valor nos muestra esta salida:

```sh
cat /sys/class/scsi_host/host*/link_power_management_policy
```

si el valor NO es "`max_performance`", toca hacer el cambio.&#x20;



Si lo que queremos es que el cambio sea temporal, basta con ejecutar esto:

```sh
sudo echo max_performance | sudo tee /sys/class/scsi_host/host*/link_power_management_policy
```

Podremos consultar en el `dmesg` si nos siguen apareciendo fallos después de ello. \
\
Si va todo correcto, podemos hacerlo permanente el cambio, de lo contrario en cuanto reiniciemos el cambio se pierde y volveremos a tener problemas.&#x20;

Para fijarlo de forma permanente, lo primero entramos en el directorio

```sh
cd /etc/udev/rules.d/
```

Creamos un fichero:

```sh
vim 50-power-save.rules
```

Con el contenido:

```sh
ACTION=="add", SUBSYSTEM=="scsi_host", KERNEL=="host*", ATTR{link_power_management_policy}="max_performance"
```

y guardamos, podemos reiniciar la maquina para comprobar que ya no tenemos mas errores en el `dmesg` ni con el dispositivo.&#x20;



Probado con kernel 6.2.0-20 en Ubuntu 23.04, y kernel 6.2.14-300 en Fedora 38 :ok\_hand:
