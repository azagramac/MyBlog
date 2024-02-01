# Borrar NVRAM

Antes de instalar un firmware Merlin, o despues de una actualizacion del firmware OEM, se debe borrar la NVRAM. \
\
Un Ejemplo de una NVRAM, el fichero es muy largo, esto solo es un fragmento.&#x20;

```bash
wps_band_x=0
wps_config_method=0xA84
wps_device_name=RT-AX88U_Pro
wps_device_pin=823764875
wps_enable=0
wps_enable_x=0
wps_mfstring=ASUSTeK Computer Inc.
wps_modelname=Wi-Fi Protected Setup Router
wps_modelnum=RT-AX88U_Pro
wps_multiband=0
wps_proc_status=0
wps_proc_status_x=0
wps_restart=0
wps_sta_pin=00000000
wps_version2=enabled
wps_wer_mode=allow
wrs_app_enable=0
wrs_app_rulelist=
wrs_cc_enable=1
wrs_cc_t=0
wrs_enable=0
wrs_mail_bit=7
wrs_mals_enable=1
wrs_mals_t=0
wrs_protect_enable=0
wrs_rulelist=
wrs_vp_enable=1
wrs_vp_t=0
x_Setting=1
zebra_enpasswd=aG9sYSBtdW5kbywgZXN0byBlcyB1biBlamVtcGxvCg==
zebra_passwd=aG9sYSBtdW5kbywgZXN0byBlcyB1biBlamVtcGxvCg==
```

Para borrarla.\
\
Botón WPS

Con el router apagado y desenchufado (botón power, encendido), pulsamos y sin soltar el botón WPS, ahora conectamos el cable de alimentación, mantenemos el botón WPS pulsado durante 30s, despues desenchufamos el cable de alimentación y soltamos el boton WPS.

Ahora ya puedes encenderlo normalmente, e instalar el firmware merlin o configurarlo nuevamente, pero no restaures la configuración!!!\
\
Por SSH, debemos previamente habilitar el ssh en el router, entramos en los ajustes del router y en "Administración / Sistema"

![](<../.gitbook/assets/image (2) (1) (1) (1) (2).png>)

Luego ya podremos entrar en el router por SSH.&#x20;

```sh
ssh user@ip_router
```

Y accederemos a el

```sh
$ ssh user@ip_router
user@ip_router's password: 

ASUSWRT-Merlin RT-AX88U_PRO 388.2_2 Sun May  7 16:35:03 UTC 2023
user@RT-AX88U_Pro:/root# 
```

Ejecutamos el comando para borrar la NVRAM

```sh
nvram erase
```

Y reiniciamos

```sh
reboot
```

