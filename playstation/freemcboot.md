# FreeMCBoot

<figure><img src="../.gitbook/assets/imagen (27).png" alt=""><figcaption></figcaption></figure>

Free McBoot es un exploit que reemplaza el menú principal de tu PS2 sin tener que modificarla físicamente y permite usar homebrews, se instala en la Memory Card (oficial a ser posible)

Lista de versiones del FMCB incluidas en el .7z:

* FreeMCBoot v1.966
* uLaunchELF v4.43a
* ESR r9b
* HD-Loader 0.8c
* Open PS2 Loader v1.1.0
* PS2-Reality 1.5 Pro

📦[Descargar FMCB v1.966](https://mega.nz/file/1BoHnDRZ#xWOI2RTTzgJTYDQB96gapxA1G-aX_3ooN-MIo5Nllug)

Es posible añadir mas opciones al menú, para ello debemos editar el fichero\
`SYS-CONF/FREEMCB.CNF` y nos encontraremos con esto.

```shell
# ----------------------------------------
# Free MCBoot Config File
# must be in mc?:/SYS-CONF/FREEMCB.CNF or mass:/FREEMCB.CNF
# ----------------------------------------
CNF_version = 1
# ----------------------------------------
Debug_Screen = 0
FastBoot = 0
ESR_Path_E1 = mass:/PS2/BOOT/ESR.ELF
ESR_Path_E2 = mc?:/BOOT/ESR.ELF
ESR_Path_E3 =
pad_delay = 0
LK_Auto_E1 = OSDSYS
LK_Circle_E1 = OSDSYS
LK_Cross_E1 = OSDSYS
LK_Square_E1 = OSDSYS
LK_Triangle_E1 = OSDSYS
LK_L1_E1 = mass:/PS2/BOOT/HDLOADER.ELF
LK_L1_E2 = mc?:/APPS/HDLOADER.ELF
LK_L1_E3 = 
LK_R1_E1 = mass:/PS2/BOOT/BOOT.ELF
LK_R1_E2 = mc?:/BOOT/BOOT.ELF
LK_R1_E3 = 
LK_L2_E1 = mass:/PS2/BOOT/PS2REALITY.ELF
LK_L2_E2 = mc?:/APPS/PS2REALITY.ELF
LK_L2_E3 = 
LK_R2_E1 = mass:/BOOT/ESR.ELF
LK_R2_E2 = mc?:/BOOT/ESR.ELF
LK_R2_E3 = 
LK_L3_E1 = OSDSYS
LK_R3_E1 = OSDSYS
LK_Up_E1 = OSDSYS
LK_Down_E1 = OSDSYS
LK_Left_E1 = OSDSYS
LK_Right_E1 = OSDSYS
LK_Start_E1 = mc?:/SYS-CONF/FMCB_CFG.ELF
LK_Start_E2 = OSDSYS
LK_Select_E1 = OSDSYS
hacked_OSDSYS = 1
OSDSYS_video_mode = AUTO
OSDSYS_Skip_Logo = 0
OSDSYS_Skip_Disc = 0
OSDSYS_Inner_Browser = 0
OSDSYS_selected_color = 0x10,0x80,0xE0,0x80
OSDSYS_unselected_color = 0x33,0x33,0x33,0x80
OSDSYS_scroll_menu = 0
OSDSYS_menu_x = 425
OSDSYS_menu_y = 175
OSDSYS_enter_x = 175
OSDSYS_enter_y = -1
OSDSYS_version_x = -1
OSDSYS_version_y = -1
OSDSYS_cursor_max_velocity = 1000
OSDSYS_cursor_acceleration = 100
OSDSYS_left_cursor = o009
OSDSYS_right_cursor = o008
OSDSYS_menu_top_delimiter =
OSDSYS_menu_bottom_delimiter =
OSDSYS_num_displayed_items = 5
OSDSYS_Skip_MC = 1
OSDSYS_Skip_HDD = 1
name_OSDSYS_ITEM_1 = Explorador de archivos
path1_OSDSYS_ITEM_1 = mc?:/BOOT/BOOT.ELF
path2_OSDSYS_ITEM_1 = mass:/PS2/BOOT/BOOT.ELF
name_OSDSYS_ITEM_2 = Juegos
path1_OSDSYS_ITEM_2 = mc?:/APPS/OPNPS2LD.ELF
path2_OSDSYS_ITEM_2 = mass:/PS2/BOOT/OPNPS2LD.ELF
```

Hay varias opciones que cambian el comportamiento de la consola al iniciarla o al iniciar un juego.

fastboot, por defecto en 1, al cambiarla a 0, hacemos que la consola cargue normalmente la animación de inicio, si la dejáramos en 1, el arranque es mas rápido al no cargar la animación.

```sh
FastBoot = 0
```

Estos parámetros, es muy parecido al anterior, cuando cargas un juego aparece la animación "playstation 2" que es un método de validación por cierto donde lo comentare en otro punto, por defecto en 1, eso no aparece.

```sh
OSDSYS_Skip_Logo = 0
OSDSYS_Skip_Disc = 0
```

Estas opciones son las que debemos modificar si queremos colocar los textos en pantalla del menú principal.

el primer parámetro, le decimos que no queremos scroll definiendo el valor a 0 (por defecto 1), los siguientes parámetros son coordenadas. por defecto las coordenadas son:

```sh
OSDSYS_menu_x = 320
OSDSYS_menu_y = 110
OSDSYS_enter_x = 30
```

Al dejarlo así, tenemos los textos de las opciones y sin scroll, mas OEM.

```sh
OSDSYS_scroll_menu = 0
OSDSYS_menu_x = 425
OSDSYS_menu_y = 175
OSDSYS_enter_x = 175
```

Estas lineas, que no contienen valor algunos, son las que añaden la versión en amarillo, el nombre de freemcboot, etc...<br>

Así seria por defecto.\
Vemos el titulo de "Free MCBoot", la versión \[Version xxxxxx], vemos unas flechas que parpadean en ambos lados de las opciones, ademas de los botones en cada extremo de la pantalla.

<figure><img src="../.gitbook/assets/imagen (29).png" alt=""><figcaption></figcaption></figure>

```
OSDSYS_menu_top_delimiter = y-99FreeMcBoot              c0[r0.80Version %VER%r0.00]y-00
OSDSYS_menu_bottom_delimiter = c0r0.60y+99Use o006/o007 to browse listy-00r0.00
```

y así un menú limpio, como es la consola originalmente pero añadiendo nuevas opciones sin romper la estética original.

<figure><img src="../.gitbook/assets/imagen (34).png" alt=""><figcaption><p>OSD</p></figcaption></figure>

```sh
OSDSYS_menu_top_delimiter =
OSDSYS_menu_bottom_delimiter =
```

En esta parte definimos las opciones que se mostraran en el OSD de la consola.

```
name_OSDSYS_ITEM_1 = Explorador de archivos
path1_OSDSYS_ITEM_1 = mc?:/BOOT/BOOT.ELF
path2_OSDSYS_ITEM_1 = mass:/PS2/BOOT/BOOT.ELF
name_OSDSYS_ITEM_2 = Juegos
path1_OSDSYS_ITEM_2 = mc?:/APPS/OPNPS2LD.ELF
path2_OSDSYS_ITEM_2 = mass:/PS2/BOOT/OPNPS2LD.ELF
```

**mc?**, es una variable donde le decimos que busque en cualquier slot de las tarjetas de memoria, podríamos concretarlo poniendo **mc0** o **mc1**.\
\
**mass**, es el almacenamiento externo por USB conectado a la consola, el formato de archivos por defecto debe ser FAT32, aunque hay un uLauncheELF modificado que aceptar ExtFAT.

Tenemos otras variables como **hdd0:**&#x70;artición , ejemplo **hdd0:\_\_sysconf**, en este caso le decimos que busque en el HDD instalado si lo tenemos en la partición \_\_sysconf el .elf a cargar.

También **cdfs**, que seria la unidad DVD de la consola y **host** seria buscar en la red
