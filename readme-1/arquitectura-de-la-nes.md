# Arquitectura de la NES

<figure><img src="../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

#### &#x20;**Especificaciones:**



| CPU            | Región | Consola       | Freq.        |
| -------------- | ------ | ------------- | ------------ |
| **Ricoh 2A03** | NTSC   | NES / Famicom | 1.789773 MHz |
| **Ricoh 2A07** | PAL    | NES PAL       | 1.662607 MHz |

La CPU de la NES está basada en el [**MOS Technology 6502**](https://es.wikipedia.org/wiki/MOS_6502) de 8-bit que trabaja a **1.78 MHz** en sistemas NTSC o **1.66 MHz** en sistemas PAL.\
\
No son un 6502 puro, es una adaptación para la NES, que incluye ademas **APU** (Audio Processing Unit).

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

#### Bus de direcciones

* **16 bits** → 64 KB direccionables
* Espacio mapeado por hardware NES

```asm
$0000–$07FF  RAM interna (2 KB)
$0800–$1FFF  Mirrors RAM
$2000–$2007  PPU registers
$4000–$4017  APU + I/O
$4020–$FFFF  Cartucho
```



#### Pinouts:

```bash
        .----\/----.
 AD1 <- |01      40| -- +5V
 AD2 <- |02      39| -> OUT0
/RST -> |03      38| -> OUT1
 A00 <- |04      37| -> OUT2
 A01 <- |05      36| -> /OE1
 A02 <- |06      35| -> /OE2
 A03 <- |07      34| -> R/W
 A04 <- |08   6  33| <- /NMI
 A05 <- |09   5  32| <- /IRQ
 A06 <- |10   0  31| -> M2
 A07 <- |11   2  30| <- TST (usually GND)
 A08 <- |12      29| <- CLK
 A09 <- |13      28| <> D0
 A10 <- |14      27| <> D1
 A11 <- |15      26| <> D2
 A12 <- |16      25| <> D3
 A13 <- |17      24| <> D4
 A14 <- |18      23| <> D5
 A15 <- |19      22| <> D6
 GND -- |20      21| <> D7
        `----------'
```

Variantes de la CPU (oficiales):

| CPU              | REGION | FECHA             |
| ---------------- | ------ | ----------------- |
| RP2A03 (E, G, H) | NTSC   | 1983-06 / 2002-11 |
| RP2A07           | PAL    | 1987-03 / 1990-04 |
| RP2A07A          | PAL    | 1991-06 / 1992-10 |



<figure><img src="../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>



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



Registros PPU (lado CPU):

| Dir   | Registro  | Detalles                  |
| ----- | --------- | ------------------------- |
| $2000 | PPUCTRL   | NMI, base NT, increment   |
| $2001 | PPUMASK   | Enable BG/Sprite          |
| $2002 | PPUSTATUS | VBlank, Sprite0, Overflow |
| $2003 | OAMADDR   | Puntero OAM               |
| $2004 | OAMDATA   | Acceso OAM                |
| $2005 | PPUSCROLL | Scroll (2 writes)         |
| $2006 | PPUADDR   | VRAM addr (2 writes)      |
| $2007 | PPUDATA   | Data VRAM                 |

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



**Memoria WRAM:**&#x20;

Tanto el Ricoh 2A03 y el MOS 6502 contienen un **bus de datos de 8 bits** y un **bus de direcciones de 16 bits**, lo que les permitía acceder hasta a **64KB de memoria**. Entonces, ¿cómo llenó Nintendo ese espacio de memoria?

Por un lado, la tarjeta madre contiene un chip que otorga **2 KB de RAM estática** (SRAM) \
Nintendo llama esta área «**Work RAM**» (WRAM) y puede usarse para almacenar:

* Variables para manejar el estado del juego y/o para buscar información.
* La «pila», la cual temporalmente guarda los valores de registros mientras que el procesador ejecuta subrutinas.
* Un «área de búfer» para que el procesador pueda copiar datos grandes entre dos ubicaciones.



Comparación entre WRAM vs VRAM:

| Aspecto    | WRAM     | VRAM                 |
| ---------- | -------- | -------------------- |
| Tamaño     | 2 KB     | 2 KB (NameTables)    |
| Acceso CPU | Directo  | Indirecto            |
| Mirroring  | Simple   | Dependiente cartucho |
| Ciclos     | 1 ciclo  | Varios + latencias   |
| DMA        | Sí (OAM) | No                   |
| Uso        | Lógica   | Vídeo                |



Acceso CPU → VRAM (registros PPU):

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

\
\
En construcción
