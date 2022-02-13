# UniFi Controller en Docker

Se puede desplegar UniFi Controller, desde el Cloud Key, o desde una aplicacion que instalas en la maquina en local, con versiones para MacOS, Linux y Win, pero... tambien puedes tenerlo en tu RaspberryPi por ejemplo, en una NanoPi...\
\
Si lo vamos a desplegar en una RaspberryPi por ejemplo, debemos instalar el servicio de docker previamente.

```
sudo apt update
sudo apt upgrade -y
sudo apt install -y libffi-dev libssl-dev python3 python3-pip
sudo curl -sSL https://get.docker.com | sh
sudo usermod -aG docker $USER
sudo apt install -y docker-compose
```

Si puedes, reiniciala despues de la instalacion.\
Bien ya tenemos docker en nuestra RaspberryPi, bienvenido al mundo de los contenedores.

Ahora vamos a descargar el repo con nuestro docker-compose para desplegar UniFi Controller.\
\
Entramos por ssh en nustra RaspberryPi y ejecutamos:

```
git clone https://github.com/AzagraMac/UnifiDocker.git
```

Entramos en el repo

```
cd UnifiDocker
```

Tendremos un fichero docker-compose.yaml, si editamos el fichero...

```
---
version: "2.1"
services:
  unifi-controller:
    image: linuxserver/unifi-controller:6.5.55
    container_name: unifi-controller
    network_mode: host
    environment:
      - PUID=1000
      - PGID=1000
      - MEM_LIMIT=1024
      - MEM_STARTUP=1024
    volumes:
      - /root/docker/unifi:/config
    ports:
      - 3478:3478/udp
      - 10001:10001/udp
      - 8080:8080
      - 8443:8443
      - 1900:1900/udp
      - 8843:8843
      - 8880:8880
      - 6789:6789
      - 5514:5514/udp
    restart: unless-stopped

networks:
  unifi_net:
    driver: bridge
```

Podemos editar por ejemplo, el volumen donde queremos que se almacene de forma persistente los datos, en el ejemplo apunta a /root/docker/unifi, puedes cambiar eso por la ruta que deseas.

y para lanzarlo

```
docker-compose up -d
```

La primera vez que lo ejecutas, tiene que descargar la imagen de Unifi, actualmente la v6.5.55 (la ultima disponible a fecha del post)... tardar unos minutos.\
Podremos ver que funciona abriendo el navegador web y poniendo la ip de la raspberrypi

```
https://IP_Raspberry:8443
```

Y listo.

![](../.gitbook/assets/img\_unifiDashboard.png)

Version actual: [6.5.55](https://www.ui.com/download/?q=6.5.55)
