# Arquitectura de la NES

<figure><img src="../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

#### <br>

La **Nintendo Entertainment System (NES)** es una **consola de videojuegos de 8 bits** lanzada por Nintendo en 1983 (Japón) y 1985 (EE. UU.).

La producción de la NES (Nintendo Entertainment System) finalizó oficialmente en distintos años según la región:

* **Japón (Famicom):** la Famicom dejó de producirse en **1995**.
* **Estados Unidos / Europa (NES):** la NES se fabricó hasta **1995 en EE. UU.**, aunque en Europa algunas unidades se vendieron hasta **1997**.

A nivel técnico:

* **CPU:** Ricoh 2A03 (NTSC) / 2A07 (PAL), derivado del MOS 6502, con APU integrada.
* **PPU:** Picture Processing Unit, coprocesador de vídeo que genera gráficos de **background y sprites**, usando tiles y paletas, totalmente independiente de la CPU.
* **Memoria RAM interna:** 2 KB (WRAM) para la CPU.
* **VRAM:** 2 KB de memoria para NameTables, usada por la PPU; el resto del patrón gráfico reside en CHR-ROM/RAM del cartucho.
* **APU:** Audio Processing Unit integrada en la CPU, 5 canales de sonido (2×pulse, 1×triangle, 1×noise, 1×DMC).
* **Lockout chip (CIC):** evita el uso de cartuchos no autorizados y controla compatibilidad regional.<br>

#### **Especificaciones técnicas:**

| CPU            | Región | Consola       | Freq.        |
| -------------- | ------ | ------------- | ------------ |
| **Ricoh 2A03** | NTSC   | NES / Famicom | 1.789773 MHz |
| **Ricoh 2A07** | PAL    | NES PAL       | 1.662607 MHz |

La CPU de la NES está basada en el [**MOS Technology 6502**](https://es.wikipedia.org/wiki/MOS_6502) de 8-bit que trabaja a **1.78 MHz** en sistemas NTSC o **1.66 MHz** en sistemas PAL.

<figure><img src="../.gitbook/assets/image (189).png" alt="2A03 vs 6502"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4).png" alt="6502" width="375"><figcaption></figcaption></figure>

El **MOS 6502** es un **microprocesador de 8 bits** lanzado en 1975, famoso por su **simplicidad, bajo coste y eficiencia**.

A nivel técnico breve:

* **Arquitectura:** 8 bits de datos, 16 bits de direccionamiento (hasta 64 KB de memoria).
* **Registros:** Acumulador (A), dos registros índice (X, Y), stack pointer (S), program counter (PC) y status (P).
* **Modo de direccionamiento:** Soporta múltiples modos (inmediato, absoluto, indirecto, relativo…).
* **Ciclo de reloj:** Generalmente 1–7 ciclos por instrucción, sin pipeline complejo ni caché.
* **Uso histórico:** Base de CPUs en consolas como NES (2A03), Atari 2600, Commodore 64 y Apple I/II.

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

La CPU de la NES, no es un 6502 puro, es una adaptación para la NES, que incluye ademas **APU** (Audio Processing Unit).

| Característica         | MOS 6502 | Ricoh 2A03/2A07      |
| ---------------------- | -------- | -------------------- |
| Modo decimal "BCD"     | ✔️       | ❌                    |
| Instrucciones ilegales | ✔️       | ⚠️ Parcial           |
| Clock fijo             | ✔️       | ❌ (derivado del PPU) |
| APU integrada          | ❌        | ✔️                   |
| Uso standalone         | ✔️       | ❌                    |

La CPU de la NES se le ha eliminado el modo **Binary-Coded Decimal** (BCD) incluido originalmente en el 6502.\
\
Algunas características que diferencian de un 6502

* El **flag D (Decimal)** existe
* Las instrucciones `SED` y `CLD` **funcionan**
* Pero el hardware **BCD** está físicamente eliminado

\
El `BCD` permite la codificación de cada dígito decimal de un número como un binario separado de 4 bits. El 6502 usa palabras de 8 bits – lo que significa que cada palabra almacena dos dígitos decimales.

Como curiosidad, el número decimal `24` se representa como:

* Binario: `00110010 00110100`
* BCD: `0010 0100`

{% hint style="info" %}
En **BCD (Binary-Coded Decimal)** cada dígito decimal se codifica por separado en 4 bits.
{% endhint %}

&#x20;

la **NES utiliza un cristal de cuarzo** para generar el reloj principal, y **varía según la región** (NTSC o PAL). A nivel técnico, esto afecta **CPU, PPU y APU**, porque todos derivan su reloj de ese cristal.

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

| Región | Frecuencia        | Función                                                          |
| ------ | ----------------- | ---------------------------------------------------------------- |
| NTSC   | **21.47727 MHz**  | Reloj para PPU y CPU/3.579545 MHz derivado para CPU/APU          |
| PAL    | **26.601712 MHz** | Reloj, derivado a 4.43361875 MHz para PPU y 1.662607 MHz CPU/APU |

\
**Derivación de reloj para la CPU**

* NTSC: CPU ≈ **1.789773 MHz**
  * Derivado del cristal maestro / 12
* PAL: CPU ≈ **1.662607 MHz**
  * Derivado del cristal maestro / 16

Esto significa que **juegos NTSC en PAL se ejecutan más lentos** (\~7 %), y la música suena más grave si no se adapta.



**Derivación de reloj para la PPU**

* NTSC: PPU ≈ **5.369318 MHz**
* PAL: PPU ≈ **5.320342 MHz**

PPU = cristal maestro / 4 en NTSC, / 5 en PAL (aproximadamente, depende de la división interna exacta del ASIC 2C02/2C07).

* Esto afecta el **frame rate**:
  * NTSC: 262 scanlines × 341 ciclos ≈ **60.1 Hz**
  * PAL: 312 scanlines × 341 ciclos ≈ **50.0 Hz**

| Sistema | Scanlines | Ciclos/frame | PPU clock    | Frame rate |
| ------- | --------- | ------------ | ------------ | ---------- |
| NTSC    | 262       | 89.342       | 5.369318 MHz | \~60.1 Hz  |
| PAL     | 312       | 106.392      | 5.320342 MHz | \~50 Hz    |

\
Convertir ciclos → frecuencia (Hz)

<figure><img src="../.gitbook/assets/image (5).png" alt="" width="491"><figcaption></figcaption></figure>



**Implicaciones**

1. **Compatibilidad de software**
   * Juegos NTSC corren más lento en PAL si no hay conversión.&#x20;
2. **Audio**
   * La frecuencia de la APU cambia con el reloj, afectando música y efectos.
3. **Timing exacto**
   * Juegos que dependen de ciclo a ciclo (sprites, scroll, glitches) se ven afectados por la frecuencia del cristal.

{% hint style="warning" %}
Las implicaciones sólo se aplican cuando la consola tiene la modificación de region free para poder jugar a juegos NTSC en consolas PAL o viceversa, en consolas sin modificación, no afecta. ya que no es posible ejecutar un juego NTSC en consola PAL o viceversa. \
\
[Como hacer el mod de region free](region-free-sin-cortar-el-cic.md)
{% endhint %}



**Bus de direcciones**

* **16 bits** → 64 KB direccionables
* Espacio mapeado por hardware NES

```asm
$0000–$07FF  RAM interna (2 KB)
$0800–$1FFF  Mirrors RAM
$2000–$2007  PPU registers
$4000–$4017  APU + I/O
$4020–$FFFF  Cartucho
```



**Pinouts:**

```bash
          ____  ____
         |*   \/    |
ROUT  <01]          [40<  VCC
COUT  <02]          [39>  $4016W.0
/RES  >03]          [38>  $4016W.1
A0    <04]          [37>  $4016W.2
A1    <05]          [36>  /$4016R
A2    <06]          [35>  /$4017R
A3    <07]          [34>  R/W
A4    <08]          [33<  /NMI
A5    <09]          [32<  /IRQ
A6    <10]   CPU    [31>  PHI2
A7    <11]          [30<  ---
A8    <12]          [29<  CLK
A9    <13]          [28]  D0
A10   <14]          [27]  D1
A11   <15]          [26]  D2
A12   <16]          [25]  D3
A13   <17]          [24]  D4
A14   <18]          [23]  D5
A15   <19]          [22]  D6
VEE   >20]          [21]  D7
         |__________|
```

**Variantes de la CPU (oficiales)**

| CPU              | REGION | FECHA             |
| ---------------- | ------ | ----------------- |
| RP2A03 (E, G, H) | NTSC   | 1983-06 / 2002-11 |
| RP2A07           | PAL    | 1987-03 / 1990-04 |
| RP2A07A          | PAL    | 1991-06 / 1992-10 |

Diagrama

<figure><img src="../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

[Ver foto en alta reolucion](https://upload.wikimedia.org/wikipedia/commons/5/5a/Nintendo-NES-Mk1-Motherboard-Top.jpg)

#### **PPU:**

La **PPU** (Picture Processing Unit) de la NES es el **chip de vídeo** encargado de **generar la imagen en tiempo real**.<br>

* Funciona **independiente de la CPU**, con su **propio reloj y bus**
* Lee **tiles y atributos** desde **CHR-ROM/RAM** y **VRAM**
* Compone cada píxel **ciclo a ciclo** mediante registros de desplazamiento
* Gestiona **scroll, paletas y sprites (OAM)**
* Produce directamente la **señal de vídeo compuesta**

La CPU **no dibuja píxeles**:\
solo **configura registros**, y la PPU hace el renderizado **por hardware**, línea a línea, **sin framebuffer**.

<figure><img src="../.gitbook/assets/image (188).png" alt="" width="563"><figcaption></figcaption></figure>

| PPU            | Región | Clock        | Frame rate | Scanlines |
| -------------- | ------ | ------------ | ---------- | --------- |
| **Ricoh 2C02** | NTSC   | 5.369318 MHz | 60Hz       | 262       |
| **Ricoh 2C07** | PAL    | 5.320342 MHz | 50Hz       | 312       |



Espacio de direcciones PPU (14 bits)

La PPU direcciona **16 KB**:

```asm
$0000–$3FFF (PPU address space)
```

{% hint style="info" %}
La CPU NO accede directamente a VRAM, todo pasa por la **PPU** (2C02 NTSC / 2C07 PAL)
{% endhint %}



**Registros PPU (lado CPU):**

| Dirección | Registro  | Descripción         | Efectos especiales / notas                                                            |
| --------- | --------- | ------------------- | ------------------------------------------------------------------------------------- |
| `$2000`   | PPUCTRL   | Control general PPU | Define NMI, base NameTable, incremento de VRAM (1/32)                                 |
| `$2001`   | PPUMASK   | Máscara / render    | Activa BG/sprites, brillo, clip de borde                                              |
| `$2002`   | PPUSTATUS | Estado PPU          | VBlank, sprite 0 hit, sprite overflow; **leer limpia VBlank y resetea latch interno** |
| `$2003`   | OAMADDR   | Dirección OAM       | Dirección de 0–255 para sprites                                                       |
| `$2004`   | OAMDATA   | Datos OAM           | Escritura/lectura de 4×64 bytes de sprites                                            |
| `$2005`   | PPUSCROLL | Scroll              | 2 escrituras: X fina, Y fina; latch interno                                           |
| `$2006`   | PPUADDR   | Dirección VRAM      | 2 escrituras: alto, bajo; latch interno                                               |
| `$2007`   | PPUDATA   | Datos VRAM          | Lectura retardada (excepto paletas); auto-incremento según PPUCTRL                    |
| `$4014`   | OAMDMA    | DMA OAM             | Copia 256 bytes de CPU→OAM; **bloquea CPU 513 ciclos**                                |

{% hint style="info" %}
* `$2000–$2007` se repite cada 8 bytes en `$2008–$3FFF`.
* Acceso a VRAM solo seguro en VBlank o con render apagado.
{% endhint %}



En la NES, **VRAM** se refiere **exclusivamente** a la **memoria de NameTables** usada por la PPU.

* **Tamaño real**: **2 KB de SRAM**
* **Tipo**: RAM estática
* **Ubicación**: dentro de la consola\
  (o en el cartucho en modo _four-screen_)
* **Accesible solo por la PPU**
* La CPU **nunca** accede directamente

{% hint style="info" %}
CHR-ROM/RAM no es VRAM
{% endhint %}



#### **Memoria WRAM:**  <a href="#memoria" id="memoria"></a>

Tanto el Ricoh 2A03 y el MOS 6502 contienen un **bus de datos de 8 bits** y un **bus de direcciones de 16 bits**, lo que les permitía acceder hasta a **64KB de memoria**. Entonces, ¿cómo llenó Nintendo ese espacio de memoria?

Por un lado, la tarjeta madre contiene un chip que otorga **2 KB de RAM estática** (SRAM) \
Nintendo llama esta área «**Work RAM**» (WRAM) y puede usarse para almacenar:

* Variables para manejar el estado del juego y/o para buscar información.
* La «pila», la cual temporalmente guarda los valores de registros mientras que el procesador ejecuta subrutinas.
* Un «área de búfer» para que el procesador pueda copiar datos grandes entre dos ubicaciones.



**Comparación entre WRAM vs VRAM:**

| Aspecto    | WRAM     | VRAM                 |
| ---------- | -------- | -------------------- |
| Tamaño     | 2 KB     | 2 KB (NameTables)    |
| Acceso CPU | Directo  | Indirecto            |
| Mirroring  | Simple   | Dependiente cartucho |
| Ciclos     | 1 ciclo  | Varios + latencias   |
| DMA        | Sí (OAM) | No                   |
| Uso        | Lógica   | Vídeo                |

**Uso de memoria mapeada**

| Dirección     | Área                                           |
| ------------- | ---------------------------------------------- |
| `$0000–$07FF` | WRAM 2 KB                                      |
| `$0800–$1FFF` | Mirrors WRAM                                   |
| `$2000–$2007` | PPU registers                                  |
| `$2008–$3FFF` | Mirrors PPU registers cada 8 bytes             |
| `$4000–$4017` | APU y I/O                                      |
| `$4018–$401F` | Test / funciones especiales (usadas raramente) |
| `$4020–$FFFF` | Cartucho (PRG-ROM, mappers)                    |

**Acceso CPU → VRAM (registros PPU)**

| Registro  | Dir   |
| --------- | ----- |
| PPUCTRL   | $2000 |
| PPUMASK   | $2001 |
| PPUSTATUS | $2002 |
| OAMADDR   | $2003 |
| OAMDATA   | $2004 |
| PPUSCROLL | $2005 |
| PPUADDR   | $2006 |
| PPUDATA   | $2007 |

#### &#x20;**APU:**&#x20;

La **APU (Audio Processing Unit)** de la NES es el **chip de sonido integrado en la CPU 2A03/2A07** que genera todo el audio de la consola.

A nivel técnico:

* Tiene **5 canales de audio**: 2 cuadrados (pulse), 1 triángulo, 1 ruido, 1 DMC (delta modulation).
* Cada canal tiene **timers, envelopes y counters** que producen tonos, percusión y samples simples.
* La CPU controla la APU a través de **registros mapeados (**`$4000–$4017`**)**.
* El **frame counter** sincroniza la actualización de todos los canales, y el DMC puede **robar ciclos a la CPU** durante la reproducción de samples.
* La salida final se mezcla mediante un **circuito analógico interno**, produciendo la característica distorsión y volumen no lineal de la NES.



**Registros APU / I/O (CPU-mapeados)**

| Dirección     | Registro                 | Descripción                         | Detalles                                                  |
| ------------- | ------------------------ | ----------------------------------- | --------------------------------------------------------- |
| `$4000–$4003` | Pulse 1                  | Control, duty, sweep, length, timer | Duty 12.5–75%; sweep complementos 1 o 2; length counter   |
| `$4004–$4007` | Pulse 2                  | Igual que Pulse 1                   | Sweep negativo distinto                                   |
| `$4008–$400B` | Triangle                 | Linear counter, length, timer       | No envelope, amplitud fija 0–15                           |
| `$400C–$400F` | Noise                    | LFSR, period, envelope, length      | Modo largo/corto, ruido percusión                         |
| `$4010–$4013` | DMC                      | Delta modulation PCM                | DMA bytes desde $8000–$FFFF, 4 ciclos CPU por byte        |
| `$4015`       | APU status / control     | Enable canales, flags IRQ           | Lectura limpia IRQ DMC, lectura actual de length counters |
| `$4016`       | Joypad 1                 | Lectura / escritura                 | Latch strobe, lectura bit a bit                           |
| `$4017`       | Joypad 2 / Frame Counter | Lectura / escritura                 | Frame counter 4/5-step, IRQ inhibit                       |

{% hint style="info" %}
* `$4015` resetea length counters al escribir.
* `$4017` define timing de la APU, crítico para música.
{% endhint %}



#### Lockout



* Es un **chip de 10 pines** dentro de la NES (2C02 / 2C07 CPU no incluido).
* Su nombre oficial: **CIC (Nintendo “Checking Integrated Circuit”)**.
* Funciona como **autenticador de cartuchos**: la consola solo arranca si detecta un cartucho con un **CIC compatible**.
* Cada región tenía **versiones distintas** (NTSC, PAL, JAP).

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

\
En construcción
