---
description: '''amdgpu: [powerplay] failed send message: GetMaxDpmFreq'''
---

# Solucion al error amdgpu: powerplay

Este es el error, se puede consultar llamando al "`dmesg`", y ocurre tanto en la v21.20 y v21.30 del driver AMD GPU en kernel anteriores a 5.8

```shell
[    6.498127] amdgpu: [powerplay] failed send message: TransferTableSmu2Dram (18) 	param: 0x00000006 response 0xffffffc2
[    6.498129] amdgpu: [powerplay] Failed to export SMU metrics table!
[    8.704702] amdgpu: [powerplay] failed send message: SetDriverDramAddrHigh (14) 	param: 0x00000080 response 0xffffffc2
[   10.913548] amdgpu: [powerplay] failed send message: TransferTableSmu2Dram (18) 	param: 0x00000006 response 0xffffffc2
[   10.913549] amdgpu: [powerplay] Failed to export SMU metrics table!
[   13.404472] amdgpu: [powerplay] failed send message: SetDriverDramAddrHigh (14) 	param: 0x00000080 response 0xffffffc2
[   15.894863] amdgpu: [powerplay] failed send message: SetDriverDramAddrHigh (14) 	param: 0x00000080 response 0xffffffc2
[   15.894865] amdgpu: [powerplay] Failed to export SMU metrics table!
[   18.384341] amdgpu: [powerplay] failed send message: SetDriverDramAddrHigh (14) 	param: 0x00000080 response 0xffffffc2
[   20.873506] amdgpu: [powerplay] failed send message: SetDriverDramAddrHigh (14) 	param: 0x00000080 response 0xffffffc2
[   20.873508] amdgpu: [powerplay] Failed to export SMU metrics table!
[   23.360578] amdgpu: [powerplay] failed send message: SetDriverDramAddrHigh (14) 	param: 0x00000080 response 0xffffffc2
[   25.848292] amdgpu: [powerplay] failed send message: SetDriverDramAddrHigh (14) 	param: 0x00000080 response 0xffffffc2
[   25.848294] amdgpu: [powerplay] Failed to export SMU metrics table!
[   28.939454] amdgpu: [powerplay] failed send message: GetMaxDpmFreq (31) 	param: 0x00000000 response 0xffffffc2
[   31.430048] amdgpu: [powerplay] failed send message: GetMaxDpmFreq (31) 	param: 0x00000000 response 0xffffffc2
[   33.920060] amdgpu: [powerplay] failed send message: GetMaxDpmFreq (31) 	param: 0x00000000 response 0xffffffc2
[   36.409185] amdgpu: [powerplay] failed send message: GetMaxDpmFreq (31) 	param: 0x00000000 response 0xffffffc2
[   38.896221] amdgpu: [powerplay] failed send message: GetMaxDpmFreq (31) 	param: 0x00020000 response 0xffffffc2
[   41.384883] amdgpu: [powerplay] failed send message: GetMaxDpmFreq (31) 	param: 0x00020000 response 0xffffffc2
[   43.863006] amdgpu: [powerplay] failed send message: GetMaxDpmFreq (31) 	param: 0x00020000 response 0xffffffc2
```

Lo primero, si la version del driver es la v21.30, tienes que hacer un downgrade a la v21.20 (dejo el link del paquete mas al final de post)\
para ello, en el directorio donde descomprimiste el driver v21.30 ejecuta

```shell
./amdgpu-pro-install --uninstall
```

Cuando termine, reinicia el equipo.\
Ahora con el driver problematico fuera, revisa la version del kernel, si es la 5.4 o cualquiera inferior a la 5.8, deberas actualizar a la 5.8 minimo.\
\
Si usas Linux Mint 20.2, por defecto trae la version 5.4\
Descarga el Kernel 5.8

```shell
wget -c https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.8/amd64/linux-headers-5.8.0-050800-generic_5.8.0-050800.202008022230_amd64.deb
wget -c https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.8/amd64/linux-image-unsigned-5.8.0-050800-generic_5.8.0-050800.202008022230_amd64.deb
wget -c https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.8/amd64/linux-headers-5.8.0-050800_5.8.0-050800.202008022230_all.deb
wget -c https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.8/amd64/linux-modules-5.8.0-050800-generic_5.8.0-050800.202008022230_amd64.deb
```

y lo instalamos

```shell
sudo dpkg -i *.deb
```

Si la instalacion termina sin errores, puedes reiniciar el equipo para cargar el nuevo kernel.

Ahora ya seria instalar los nuevos drivers v21.20 de AMDGPU

Te dejo los enlaces de ambas versiones por si cuando leas este post, ya no es posible descargarlas desde la web de AMD\
Version [21.20](https://mega.nz/file/RR5EjBAb#tPajDSXZbeKsHZFdVUtUhYLRdPpEU89H3Uw9x4VSudw)\
Version [21.30](https://mega.nz/file/Zd4AhLZK#DXEL\_1BDNtPYFZ8HDFcT2dDdYbNyUnQQp0vSBuuI77g)
