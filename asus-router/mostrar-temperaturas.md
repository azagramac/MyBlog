# Mostrar temperaturas

Podemos ver las temperaturas de la CPU, y wifi 2.4Ghz y 5Ghz. \
\
Entrar por SSH en el router

```sh
ssh user@ip_router
```

Ver temperatura CPU

```sh
cat /sys/class/thermal/thermal_zone0/temp | awk '{print $1 / 1000}'
```

Nos mostrara el valor numerico en celsius

```bash
51.531
```



Para ver la temp de la red 2.4/5Ghz, podemos consultar en la NVRAM el dispositivo.&#x20;

```sh
nvram get wl0_ifname
nvram get wl1_ifname
```

y nos mostrara el nombre del dispositivo correspondiente.&#x20;



Temperatura red 2.4Ghz

```sh
wl -i eth6 phy_tempsense | awk '{print $1 / 2 + 20}'
```

Temperatura red 5Ghz

```sh
wl -i eth7 phy_tempsense | awk '{print $1 / 2 + 20}'
```
