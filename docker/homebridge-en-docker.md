---
description: >-
  Para quienes no lo sepan, Homebridge es un software para hacer compatible
  dispositivos no certificados ni pensados para Homekit.
---

# HomeBridge en Docker

![](../.gitbook/assets/homebridge-lib.png)

Vamos desplegar un contenedor docker con Homebridge, podemos hacerlo en nuestro equipo o en una RaspberryPi/NanoPi/Odroid...&#x20;

Necesitamos tener instalado en la maquina, el servicio de docker.&#x20;

```
sudo apt update
sudo apt upgrade -y
sudo apt install -y libffi-dev libssl-dev python3 python3-pip
sudo curl -sSL https://get.docker.com | sh
sudo usermod -aG docker pi
sudo apt install -y docker-compose
```

Ahora simplemente descargamos el repositorio.

```
git clone https://github.com/AzagraMac/homebridge.git
```

Con el repo ya en nuestro equipo, entramos en el directorio y levantamos el contenedor, la primera vez tardara un poco ya que tiene que descargar la imagen.&#x20;

```
cd homebridge
docker-compose up -d
```

Podemos ver su log

```
docker logs homebridge
```

Cuando termine de descargar la imagen, desde el navegador, entramos en el dashboard.

```
http://IP_MAQUINA:9590
```

![](<../.gitbook/assets/Captura de pantalla 2022-03-01 a las 19.01.15.png>)

Datos de acceso:

```
user: admin
pass: admin
```
