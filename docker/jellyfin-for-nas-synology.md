# Jellyfin for NAS Synology

<figure><img src="../.gitbook/assets/imagen (50).png" alt=""><figcaption></figcaption></figure>

Jellyfin desplegado en un contenedor en NAS Synology (DS423+) con DSM 7.2 y aceleración por hardware habilitada.&#x20;

Lo primero es instalar el modulo en el NAS si no lo tenemos.&#x20;

Entramos en "Centro de paquetes" y buscamos "Container manager" o "docker" y lo instalamos.&#x20;

<figure><img src="../.gitbook/assets/imagen (53).png" alt=""><figcaption></figcaption></figure>



Abrimos Container manager y seleccionamos  "**Proyecto**"

<figure><img src="../.gitbook/assets/imagen (55).png" alt=""><figcaption></figcaption></figure>



Pegamos el código de docker-compose

```yaml
version: "3.9"

services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    restart: always
    user: 1027:100
    group_add:
      - "937"
    volumes:
      - /volume1/docker/jellyfin/config:/config:rw
      - /volume1/docker/jellyfin/cache:/cache:rw
      - /volume1/video:/media:rw    
    environment:
      - PUID=1027
      - PGID=100
      - TZ=Europe/Madrid
      - JELLYFIN_FFMPEG=/usr/lib/jellyfin-ffmpeg/ffmpeg
      - JELLYFIN_WEB_DIR=/jellyfin/jellyfin-web
      - JELLYFIN_DATA_DIR=/config
      - JELLYFIN_CACHE_DIR=/cache
      - JELLYFIN_CONFIG_DIR=/config/config
      - JELLYFIN_LOG_DIR=/config/log
      - XDG_CACHE_HOME=/cache
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128
    ports:
      - 8096:8096/tcp
      - 8920:8920/tcp
      - 7359:7359/udp
    healthcheck:
      test: curl --fail http://localhost:8096/health || exit 1
      interval: 45s
      timeout: 30s
      retries: 3
    mem_limit: 10240m

networks:
  jellyfin:

```

{% hint style="warning" %}
Ajusta `volumes` para que monte las unidades que corresponda y el `mem_limit: 10240m`, en este ejemplo son 10Gb de ram, tengo 18Gb en el NAS, ajústalo a tu hardware y el `user` que corresponda en tu caso.
{% endhint %}

Una vez creado descargara la imagen y levantara el contenedor.&#x20;

<figure><img src="../.gitbook/assets/imagen (56).png" alt=""><figcaption></figcaption></figure>

Ya le indicamos el dispositivo para habilitar la aceleración por hardware.

```yaml
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128
```

Si tenemos otro dispositivo, podemos verificar cual es:

```bash
$ ls -l /dev/dri/
crw------- 1 root root 226, 0 Sep 4 08:00 card0
crw-rw---- 1 root videodriver 226, 128 Sep 4 08:00 renderD128
```

Y nos quedaría conocer el ID del grupo videodriver, que en este caso es el 937, lo declaramos en el docker-compose en `group_add`

```bash
$ grep videodriver /etc/group
videodriver:x:937:
```

Podemos ver su estado.

<figure><img src="../.gitbook/assets/imagen (57).png" alt=""><figcaption></figcaption></figure>



Entramos en el interfaz web de jellyfin `http://IP-NAS:8096/web/#/dashboard/playback/transcoding`

Entramos en "**Panel de control**"

<figure><img src="../.gitbook/assets/Captura desde 2025-09-04 18-01-56.png" alt=""><figcaption></figcaption></figure>

Seleccionamos en "**Reproducción**" y dentro "**Conversión**"

<figure><img src="../.gitbook/assets/Captura desde 2025-09-04 18-02-19.png" alt=""><figcaption></figcaption></figure>

En "**Acceleración por hardware**" seleccionamos Intel QuickSync (QSV) y en QS Device `/dev/dri/renderD128`

Marcamos en "**Activar decodificación por hardware para**"

* ✅ H264
* ✅ HEVC
* ✅MPEG2
* ✅ VC1
* ✅ VP8
* ✅ VP9
* ❌ AV1
* ✅ HEVC 10bit
* ✅ VP9 10bit
* ✅ HEVC RExt 8/10bit
* ❌ HEVC RExt 12bit



✅ Preferir decodificadores de hardware DXVA o VA-API nativos del sistema operativo

#### Opciones de codificación por hardware

* ✅ Activar codificación por hardware&#x20;
* ❌ Habilitar el codificador hardware H.264 de bajo consumo de Intel
* ❌ Habilitar el codificador hardware HEVC de bajo consumo de Intel

#### Opciones de formato de codificación

* ✅ Permitir la codificación en formato HEVC
* ❌ Permitir encodificación en formato AV1

{% hint style="danger" %}
No marques ninguna opción de AV1 ya que en contenido con 4K HEVC por ejemplo, dará problemas de reproducción&#x20;
{% endhint %}



Bajamos al final de la pagina y le damos a "**Guardar**", reiniciaremos el contenedor desde "**Container manager**"
