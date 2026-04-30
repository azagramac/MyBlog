# Arquitectura NeoGeo AES

<figure><img src="../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

#### Especificaciones técnicas

### 🎮 NEOGEO AES — Información técnica estructurada

#### 🧠 1. Arquitectura del sistema

* 🏗️ **Plataforma:** SNK Multi Video System (MVS) adaptada a hogar (AES)
* 🧩 **CPU principal:** Motorola 68000 @ 12 MHz
* ⚙️ **CPU secundaria (sonido):** Zilog Z80 @ 4 MHz
* 🔊 **Chip de sonido:** Yamaha YM2610
  * Síntesis FM (YM2610 + ADPCM-A/B)
  * 15 canales de audio combinados (FM + samples)

***

#### 🎨 2. Sistema gráfico

* 🖼️ **Resolución base:** 320×224 píxeles
* 🎨 **Paleta total:** hasta 65.536 colores
* 🌈 **Colores simultáneos en pantalla:** hasta 4.096
* 🧱 **Sprites:**
  * Hasta \~380 sprites en pantalla (dependiendo de tamaño)
  * Tamaño variable (8×8 hasta 16×512 px)
* 🧩 **Planos de scroll:** múltiples capas (background + foreground)
* ⚡ Hardware especializado para animaciones sin sobrecargar CPU

***

#### 💾 3. Memoria y almacenamiento

* 🧠 **RAM principal:** \~64 KB (work RAM)
* 🎮 **VRAM:** dedicada al subsistema gráfico
* 💿 **Formato de juegos:** cartuchos ROM
* 📦 **Capacidad de cartuchos:**
  * Desde \~46 Mbit hasta más de 716 Mbit (y superiores en títulos tardíos)
* 💾 **Memory Card:** 2 KB, nuevas 16 KB
  * Guardado de progreso y récords
  * Persistencia entre juegos compatibles

***

#### 🎮 4. Sistema de cartuchos

* 🔌 **Conector propietario NEOGEO AES**
* 🔁 Compatibilidad:
  * AES ↔ MVS (con adaptaciones físicas)
* 🧩 Arquitectura dividida en chips:
  * Program ROM (CPU)
  * Sound ROM
  * Graphic ROM (sprites y tiles)

***

#### 🔊 5. Audio

* 🎵 **Motor principal:** Yamaha YM2610
* 🎚️ Características:
  * FM synthesis (instrumentos complejos)
  * PCM samples (efectos y voces)
  * ADPCM para audio comprimido
* 🔉 Salida estéreo

***

### 📺 6. Salida de vídeo (corregido)

* 🧷 **Salida principal: RGB analógico (RGBS)**
  * Señal RGB + sincronía compuesta (Sync on Composite / CSYNC según cableado)
* 🔌 **Conector estándar: DIN-8 (NEOGEO AES)**
  * Pinout propietario SNK para vídeo RGB + audio
* 📼 **Señal disponible según cableado/adaptador:**
  * 🎨 **RGB (calidad arcade / profesional) — nativa**
  * 📺 **Compositado (CVBS) — derivado mediante cable o conversión externa**
  * 📡 **S-Video — NO nativo (solo mediante modificaciones o adaptadores externos)**

***

#### 🎮 7. Controles

* 🎮 Puertos: 2 mandos (DB15 propietario)
* 🕹️ Tipo de control:
  * Joystick digital de microswitch
  * 4 botones principales (A, B, C, D)
* ⚙️ Expansión: algunos periféricos arcade stick oficiales

***

#### 🌍 8. Bloqueo regional

* 🧭 Implementado principalmente en la **BIOS del sistema**
* 🌐 Regiones típicas:
  * 🇯🇵 Japón
  * 🇺🇸 USA
  * 🌍 Export / Asia (según BIOS)
* ⚙️ Efectos del bloqueo:
  * Cambios en idioma (Japonés/Inglés)
  * Modificación de contenido (ej. sangre vs “sweat” en algunos juegos)
  * Ajustes de dificultad y comportamiento de juegos
  * Mensajes de error o limitaciones en algunos títulos si región no coincide

***

#### 🧠 9. BIOS como punto de control

* 🧩 La **BIOS es el único “punto central” de lógica de control**
* Funciones:
  * Inicialización del sistema
  * Menú de servicio (DIP settings)
  * Gestión de memory card
  * Rutinas de arranque de cartucho
* 🔓 Importante:
  * La BIOS puede ser reemplazada (ej. UniBIOS)
  * Esto elimina prácticamente todas las restricciones regionales

***

#### ⚙️ 10. DIP switches

* 🔧 Interruptores físicos en la placa o accesibles vía menú
* Controlan:
  * Región lógica (en combinación con BIOS)
  * Modo arcade vs home
  * Dificultad base
  * Configuración de sistema (coin settings en MVS, limitado en AES)

***

#### 💾 11. Memory Card (seguridad de datos)

* 💾 Sistema de guardado externo simple
* 🔐 No cifrado:
  * Datos almacenados en formato propietario pero no seguro
  * Puede ser leído/modificado con herramientas externas



#### Fotos

<figure><img src="../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

