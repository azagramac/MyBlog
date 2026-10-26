# Automatización y Monitorización de WireGuard en Debian 13

Este manual técnico documenta la configuración del cliente **WireGuard** para automatizar su conexión en entornos externos (WiFi y Ethernet). La VPN se mantendrá apagada únicamente dentro de la red local de casa.

***

### 🛠️ 1. Requisitos y Dependencias

Antes de comenzar, es necesario instalar las herramientas del sistema para gestionar WireGuard y permitir que los scripts de automatización interactúen con NetworkManager de forma segura.

Abre una terminal y ejecuta:

```bash
sudo apt update
sudo apt install -y wireguard-tools network-manager-wireguard
```

***

### 📥 2. Importación del Perfil VPN

NetworkManager lee directamente archivos de configuración estándares de WireGuard (`.conf`). Para importar tu perfil con el nombre **Home**:

1. Sitúate en la carpeta donde se encuentra tu archivo (ej. `NOMBRE_PERFIL_WIREGUARD.conf`).
2.  Ejecuta el comando de importación:

    ```bash
    sudo nmcli connection import type wireguard file NOMBRE_PERFIL_WIREGUARD.conf
    ```
3.  Verifica que el sistema ha registrado la conexión correctamente:

    ```bash
    nmcli connection show
    ```

    _Deberías ver listado el nombre_ **`NOMBRE_PERFIL_WIREGUARD`** _bajo el tipo `wireguard`._

***

### ⚙️ 3. Script de Automatización (NetworkManager Dispatcher)

Para gestionar la conexión de forma inteligente tanto por **WiFi** como por **cable Ethernet**, utilizaremos un script de eventos global. La VPN se **apagará** si detecta el SSID `TU_SSID_WIFI` **O** si recibe cualquier dirección IP perteneciente al rango doméstico `192.168.1.X`. En cualquier otra red, la VPN se **encenderá** sola.

#### 📝 Instrucciones de Instalación:

1.  Crea el archivo del script en el directorio de automatización de red:

    ```bash
    sudo vim /etc/NetworkManager/dispatcher.d/99-wireguard-auto.sh
    ```
2. Pega el siguiente código optimizado:

```bash
#!/bin/bash

# --- CONFIGURACIÓN ---
WIFI_CASA="TU_SSID_WIFI"
RED_CASA_PREFIJO="TU_RED_IP, ej. 192.168.1."
NOMBRE_VPN="NOMBRE_PERFIL_WIREGUARD"  # Ajusta al nombre exacto de tu perfil WireGuard en nmcli
# ---------------------

INTERFAZ="$1"
ACCION="$2"

# 1. FILTRADO INICIAL: Solo actuar ante conexiones (up) de interfaces físicas (Ethernet o WiFi)
# Ignoramos la propia interfaz virtual de WireGuard (ej. wg0) o el bucle local (lo)
if [ "$ACCION" != "up" ] || [[ "$INTERFAZ" =~ ^(wg|tun|vpn|lo|br-|docker) ]]; then
    exit 0
fi

# Esperar un instante breve para asegurar que la negociación DHCP y nmcli ha terminado por completo
sleep 1

# 2. COMPROBACIÓN GLOBAL DE ENTORNO
ESTA_EN_CASA=false

# A. Comprobar si alguna de las IPs asignadas actualmente en el sistema pertenece a casa
if ip -o -4 addr show | awk '{print $4}' | grep -q "^${RED_CASA_PREFIJO}"; then
    ESTA_EN_CASA=true
fi

# B. Comprobar si el WiFi actualmente activo y conectado se llama "Ovni"
WIFI_ACTUAL=$(nmcli -t -f active,ssid connection show | grep '^yes:' | cut -d: -f2- | head -n1)
if [ "$WIFI_ACTUAL" = "$WIFI_CASA" ]; then
    ESTA_EN_CASA=true
fi

# 3. COMPROBACIÓN DE ESTADO DE LA VPN (Evita comandos redundantes)
# Verificamos si la VPN ya está activa en NetworkManager
VPN_ACTIVA=$(nmcli -t -f active,name connection show | grep "^yes:${NOMBRE_VPN}$")

# --- LÓGICA DE DECISIÓN AFINADA ---
if [ "$ESTA_EN_CASA" = true ]; then
    # Si detecta que estás en casa pero la VPN está encendida, la apaga
    if [ -n "$VPN_ACTIVA" ]; then
        nmcli connection down id "$NOMBRE_VPN" >/dev/null 2>&1
    fi
else
    # Si estás fuera de casa y la VPN está apagada, la enciende
    if [ -z "$VPN_ACTIVA" ]; then
        nmcli connection up id "$NOMBRE_VPN" >/dev/null 2>&1
    fi
fi
```

3. Guarda los cambios con `Ctrl + O`, confirma con `Enter` y sal con `Ctrl + X`.
4.  **🔒 Muy Importante (Permisos de Seguridad):** Asigna los permisos correctos para que NetworkManager pueda ejecutar el script:

    ```bash
    sudo chmod +x /etc/NetworkManager/dispatcher.d/99-wireguard-auto.sh
    sudo chown root:root /etc/NetworkManager/dispatcher.d/99-wireguard-auto.sh
    ```

***

### 🧪 4. Protocolo de Pruebas

Para validar el correcto comportamiento del entorno sin salir de casa, realiza los siguientes tests simulados:

* **🏠 Test 1: Conexión en Red Doméstica**
  1. Conéctate al WiFi `TU_SSID_WIFI` o pincha el cable Ethernet de casa (Asegúrate de tener una IP de tu red local).
  2. Intenta encender la VPN manualmente desde Cinnamon o con `nmcli connection up NOMBRE_PERFIL_WIREGUARD`.
  3. _Resultado esperado:_ La VPN debe apagarse automáticamente tras 1 o 2 segundos.
* **📱 Test 2: Conexión en Red Externa**
  1. Comparte datos móviles desde tu teléfono inteligente (creando un punto de acceso con un nombre distinto a `TU_SSID_WIFI`).
  2. Conecta el pc/portatil a esta nueva red inalámbrica.
  3. _Resultado esperado:_ La VPN `NOMBRE_PERFIL_WIREGUARD` debe encenderse de manera automática en segundo plano sin intervención del usuario.

***

### 📊 5. Monitorización del Estado del Túnel

Dado que WireGuard es un protocolo "`sin estado`" (no mantiene sesiones abiertas ni envía respuestas ping constantes), la salud de la conexión se audita mediante el tráfico dinámico.

#### 💻 A. Monitorización por Consola (Terminal)

El comando definitivo para comprobar la salud real del enlace apunta directamente al nombre de la interfaz importada:

```bash
sudo wg show NOMBRE_PERFIL_WIREGUARD
```

* 🟢 **Conexión Viva:** Verás la línea `latest handshake:` indicando actividad hace escasos segundos o minutos, junto con el contador de bytes en `transfer:`.
* 🔴 **Conexión Caída/Bloqueada:** Si la línea `latest handshake` no aparece o muestra horas/días de antigüedad, el túnel está levantado a nivel local pero no logra comunicarse con el servidor remoto.

> 💡 **Tip:** Deja el monitor corriendo en tiempo real cada 2 segundos usando: `sudo watch wg show Home`

#### 🖥️ B. Monitorización Gráfica (Entorno Cinnamon)

Para disponer de un control visual cómodo directo en el escritorio:

1. Haz clic derecho sobre tu barra de tareas inferior y selecciona **Añadir applets al panel**.
2. Descarga e instala el applet **Network Data Usage** (o _Monitor de Red_).
3. Configúralo en las opciones para vigilar la interfaz virtual `NOMBRE_PERFIL_WIREGUARD`. Podrás ver gráficas de velocidad de subida y descarga en tiempo real integradas junto al reloj del sistema.
