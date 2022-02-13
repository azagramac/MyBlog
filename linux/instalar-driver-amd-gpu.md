# Instalar driver AMD GPU

## Instalar driver AMD GPU

![](../.gitbook/assets/img\_radeonLinux.png)

Lo primero, es descargarnos el driver desde la web AMD [https://www.amd.com/en/support](https://www.amd.com/en/support), elegiremos nuestra GPU y nos descargamos el fichero.

![](../.gitbook/assets/img\_downloadDriverRadeon.png)

nos dara varios sistemas operativos para nuestra grafica, elegimos el nuestro, en mi caso, Ubuntu x86 64bit

![](../.gitbook/assets/img\_downloadDriverRadeon2.png)

y descargamos el paquete.

![](../.gitbook/assets/img\_downloadDriverRadeon3.png)

Con el fichero ya en nuestro equipo, los descomprimimos, y vemos que contiene muchos paquetes .deb y unos scripts de shell.

Para la instalacion tenemos varias formas

| **Command**                                                 | **Installed Components**                                                                                                                    |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `$ ./amdgpu-pro-install -y`                                 | <ul><li>Base kernel</li><li>Accelerated graphics</li><li>Mesa multimedia</li><li>Pro OpenGL</li><li>Pro Vulkan</li></ul>                    |
| `$ ./amdgpu-pro-install -y --opencl=rocr,legacy`            | <ul><li>Base kernel</li><li>Accelerated graphics</li><li>Mesa multimedia</li><li>Pro OpenGL</li><li>Pro Vulkan</li><li>Pro OpenCL</li></ul> |
| `$ ./amdgpu-pro-install -y --opencl=rocr,legacy --headless` | <ul><li>Only base kernel</li><li>Pro OpenCL (headless mode)</li></ul>                                                                       |

personalmente, siempre elijo la opcion pro con rocs y legacy, que incluye todo, desde la API de Vukan, OpenCL, y OpenGL

Antes de proceder a la instalacion, vamos a actualizar el sistema

```bash
sudo apt update && sudo apt -y upgrade
```

```bash
./amdgpu-pro-install -y --opencl=rocr,legacy
```

Y listo, reiniciamos el sistema y ya tenemos instalado el driver AMD GPU
