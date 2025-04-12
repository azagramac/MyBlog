# Possible missing firmware /lib/firmware/amdgpu

<figure><img src="../.gitbook/assets/imagen (49).png" alt=""><figcaption></figcaption></figure>

Clonar el repositorio de linux-firmware

```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git
```

de la lista que nos da el error, la copiamos, dejando solo el nombre del binario que nos falta. \
\
Ejemplo, estos son los binarios que me indicaba que no los tenia:

```bash
W: Possible missing firmware /lib/firmware/amdgpu/ip_discovery.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/vega10_cap.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/sienna_cichlid_cap.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/navi12_cap.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/psp_13_0_11_ta.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/psp_13_0_11_toc.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/psp_13_0_10_ta.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/psp_13_0_10_sos.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/aldebaran_cap.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_3_imu.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_4_rlc.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_4_mec.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_4_me.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_4_pfp.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_3_rlc.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_3_mec.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_3_me.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_3_pfp.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_0_toc.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/sdma_6_0_3.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/sienna_cichlid_mes1.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/sienna_cichlid_mes.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/navi10_mes.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_4_mes1.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_4_mes_2.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_4_mes.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_3_mes1.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_3_mes_2.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_3_mes.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_2_mes_2.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_1_mes_2.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/gc_11_0_0_mes_2.bin for module amdgpu
W: Possible missing firmware /lib/firmware/amdgpu/smu_13_0_10.bin for module amdgpu
```

Creamos el fichero, dejando solo el nombre del binario y lo guardamos donde queramos, puede ser mismamente dentro de, ejemplo: `/home/$USER/linux-firmware/amdgpu`

```bash
ip_discovery.bin
vega10_cap.bin
sienna_cichlid_cap.bin
navi12_cap.bin
psp_13_0_11_ta.bin
psp_13_0_11_toc.bin
psp_13_0_10_ta.bin
psp_13_0_10_sos.bin
aldebaran_cap.bin
gc_11_0_3_imu.bin
gc_11_0_4_rlc.bin
gc_11_0_4_mec.bin
gc_11_0_4_me.bin
gc_11_0_4_pfp.bin
gc_11_0_3_rlc.bin
gc_11_0_3_mec.bin
gc_11_0_3_me.bin
gc_11_0_3_pfp.bin
gc_11_0_0_toc.bin
sdma_6_0_3.bin
sienna_cichlid_mes1.bin
sienna_cichlid_mes.bin
navi10_mes.bin
gc_11_0_4_mes1.bin
gc_11_0_4_mes_2.bin
gc_11_0_4_mes.bin
gc_11_0_3_mes1.bin
gc_11_0_3_mes_2.bin
gc_11_0_3_mes.bin
gc_11_0_2_mes_2.bin
gc_11_0_1_mes_2.bin
gc_11_0_0_mes_2.bin
smu_13_0_10.bin
```

Y ahora lanzamos un bucle `for`, para que copie cada archivo de esa lista que tenemos en el repositorio que hemos descargado en la ruta `/lib/firmware/amdgpu/`.

```bash
for file in $(<lista.txt); do sudo cp "$file" /lib/firmware/amdgpu/; done
```

y actualizamos el `initramfs` con los nuevos cambios

```bash
sudo update-initramfs -k all -u
```

{% hint style="info" %}
Seguramente que falte algún binario por copiar.
{% endhint %}

Y reiniciamos

