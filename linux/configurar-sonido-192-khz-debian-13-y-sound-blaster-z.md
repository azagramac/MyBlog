# Configurar sonido 192 kHz Debian 13 y Sound Blaster Z

<figure><img src="../.gitbook/assets/image (171).png" alt="" width="375"><figcaption></figcaption></figure>

\
Lo primero es instalar el firmware

Descargamos el .bin desde aqui: [https://packages.debian.org/file%3Actefx-desktop.bin](https://packages.debian.org/file%3Actefx-desktop.bin)\
o desde aquí si falla el anterior: [https://mega.nz/file/hcQVDDJL#B3QkvwyUkHSDwN-7C9tKndipYyuGQioQMO64oyvCEEU](https://mega.nz/file/hcQVDDJL#B3QkvwyUkHSDwN-7C9tKndipYyuGQioQMO64oyvCEEU)

Lo copiamos a:

```bash
sudo cp -rf ctefx-desktop.bin /usr/lib/firmware/
```

**Instalamos el paquete**

```bash
sudo apt install -y firmware-linux-nonfree
```



**Verificamos que el sistema reconoce nuestra tarjeta de sonido**

```bash
aplay -l
```

output:

```bash
$ aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: Creative [HDA Creative], device 0: CA0132 Analog [CA0132 Analog]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: Creative [HDA Creative], device 1: CA0132 Digital [CA0132 Digital]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

y

```bash
pw-cli list-objects | grep -i 'sound blaster'
```

output:

```bash
$ pw-cli list-objects | grep -i 'sound blaster'
 		device.description = "CA0132 Sound Core3D [Sound Blaster Recon3D / Z-Series / Sound BlasterX AE-5 Plus] (SB1570 SB Audigy Fx)"
 		node.description = "CA0132 Sound Core3D [Sound Blaster Recon3D / Z-Series / Sound BlasterX AE-5 Plus] (SB1570 SB Audigy Fx) Estéreo analógico"
 		node.description = "CA0132 Sound Core3D [Sound Blaster Recon3D / Z-Series / Sound BlasterX AE-5 Plus] (SB1570 SB Audigy Fx) Estéreo analógico"
```

Comprobamos su estado:

```bash
pactl list short sinks
```

output:

```bash
$ pactl list short sinks
	  alsa_output.pci-0000_07_00.0.analog-stereo	PipeWire	s32le 2ch 48000Hz	SUSPENDED
	  alsa_output.pci-0000_0d_00.1.hdmi-stereo	PipeWire	s32le 2ch 48000Hz	SUSPENDED
```



Llegados a este punto, Debian 13, reconoce perfectamente la tarjeta de sonido Sound Blaster Z.\
✅ 🎉

Se puede apreciar que:

* PipeWire la está usando como **alsa\_output.pci-0000\_07\_00.0.analog-stereo**
* Actualmente está en **s32le 2ch 48000Hz** (32-bit, 48 kHz), **no en 24/192 kHz**



{% hint style="info" %}
Debian 13, PipeWire usa `wireplumber` como session manager
{% endhint %}

#### **Configurar PipeWire para 24/192 kHz**

```bash
mkdir -p ~/.config/pipewire/pipewire.conf.d
vim ~/.config/pipewire/pipewire.conf.d/override.conf
```

contenido:

```bash
context.exec = [
    { path = "/usr/bin/pipewire" args = [] }
]

default.clock.rate          = 192000
default.clock.allowed-rates = [ 44100, 48000, 88200, 96000, 176400, 192000 ]
resample-method             = "none"
```

<pre class="language-bash"><code class="lang-bash"><strong>vim ~/.config/pipewire/alsa-monitor.conf
</strong></code></pre>

contenido:

```bash
context.modules = [
    { name = libpipewire-module-alsa-card
      args = {
          card.name = "hw:0"
          device.names = [ "analog-stereo" ]
          ignore-channels = false
          channels.min = 2
          channels.max = 2
          # Forzar 24-bit / 192kHz
          default.clock.rate = 192000
          default.clock.quantum = 1024
          default.clock.quantum-min = 32
          default.clock.quantum-max = 8192
      }
    }
]
```

{% hint style="info" %}
Esto fuerza que PipeWire permita hasta 192 kHz y **desactiva resampling automático**.
{% endhint %}



Reinicia PipeWire y WirePlumber:

```bash
systemctl --user restart pipewire pipewire-pulse wireplumber
```

#### **Crear override de WirePlumber (ALSA)**

PipeWire **no se configura directamente** desde `pipewire.conf` para ALSA por tarjeta concreta.\
En Debian 13, **la política la controla WirePlumber**, así que el override correcto es **WirePlumber → ALSA monitor**

```bash
mkdir -p ~/.config/wireplumber/wireplumber.conf.d
vim ~/.config/wireplumber/wireplumber.conf.d/99-sbz-192khz.conf
```

contenido:

```bash
monitor.alsa.rules = [
  {
    matches = [
      {
        device.name = "alsa_card.pci-0000_07_00.0"
      }
    ]

    actions = {
      update-props = {
        audio.format = "S32LE"
        audio.rate = 192000
        audio.channels = 2
        api.alsa.period-size = 1024
        api.alsa.headroom = 0
        session.suspend-timeout-seconds = 0
      }
    }
  }
]

```

*   `device.name` coincide **exactamente** con:

    ```bash
    alsa_output.pci-0000_07_00.0.analog-stereo
    ```
* Se fuerza:
  * **S32LE** (formato que _sí_ soporta el CA0132)
  * **192 kHz**
  * **2 canales**
* Se evita suspensión del sink
* No hay resampling interno si la app usa 192 kHz

```bash
systemctl --user restart wireplumber
systemctl --user restart pipewire pipewire-pulse
```

Verificamos:

```bash
pactl list short sinks
```

output:

```bash
$ pactl list short sinks
    alsa_output.pci-0000_07_00.0.analog-stereo PipeWire s32le 2ch 192000Hz SUSPENDED
    alsa_output.pci-0000_0d_00.1.hdmi-stereo PipeWire s32le 2ch 48000Hz SUSPENDED
```

* Analog-stereo (Sound Blaster Z) → 192 kHz ✅
* HDMI (GPU) → 48 kHz ❌

Esto es **normal** y esperado: el override solo apuntaba a **hw:0 / analog-stereo**, por eso PipeWire fuerza 192 kHz en esa tarjeta. La salida HDMI (hw:1) no tiene configuración especial, así que se queda en 48 kHz, que es la frecuencia por defecto de PipeWire para sinks no configurados.

#### Ver formatos soportados por el DAC

```bash
aplay -D hw:0,0 --dump-hw-params /dev/zero
```

output:

```bash
$ aplay -D hw:0,0 --dump-hw-params /dev/zero
Playing raw data '/dev/zero' : Unsigned 8 bit, Rate 8000 Hz, Mono
HW Params of device "hw:0,0":
--------------------
ACCESS:  MMAP_INTERLEAVED RW_INTERLEAVED
FORMAT:  S16_LE S32_LE
SUBFORMAT:  STD MSBITS_MAX
SAMPLE_BITS: [16 32]
FRAME_BITS: [32 192]
CHANNELS: [2 6]
RATE: [16000 192000]
PERIOD_TIME: (31 16384000]
PERIOD_SIZE: [6 262144]
PERIOD_BYTES: [128 2097152]
PERIODS: [2 32]
BUFFER_TIME: (62 32768000]
BUFFER_SIZE: [12 524288]
BUFFER_BYTES: [128 2097152]
TICK_TIME: ALL
--------------------
aplay: set_params:1387: Sample format non available
Available formats:
- S16_LE
- S32_LE
```

\
Resumen de esa salida:

**Formatos soportados por el DAC (hardware real)**

```bash
FORMAT:  S16_LE S32_LE
SAMPLE_BITS: [16 32]
```

El CA0132 **NO soporta**:

* `S24_LE`
* `S24_3LE` (24-bit packed en 3 bytes)

Solo admite:

* **16 bits**
* **32 bits**

**Frecuencias soportadas**

```bash
RATE: [16000 192000]
```

✅ El DAC **sí soporta 192 kHz nativos**.\
No hay emulación ni limitación por driver.

**Canales**

```bash
CHANNELS: [2 6]
```

Soporta:

* Estéreo (2)
* Multicanal (5.1) 🔊
