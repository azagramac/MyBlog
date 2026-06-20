---
icon: microchip
cover: ../.gitbook/assets/photo_2026-06-20_02-00-52.jpg
coverY: -2.2107081174438687
---

# Grabar EPROM

Material necesario:\
\- [Borrador de EPROM](https://es.aliexpress.com/item/1005006520190711.html) (EPROM Eraser)\
\- [Programador EPROM](https://es.aliexpress.com/item/1005008646900797.html) (Xgecu T48)\
\- Chips EPROM, en este ejemplo, [AM27C1024](https://es.aliexpress.com/item/1005012031312001.html)\
\- [Software Xgpro](http://www.xgecu.com/EN/download.html?refreshed=1781909489908), actualmente [v13.16](https://www.mediafire.com/folder/hfg5kfj7euw5j/xgecu)



#### 1. Borrado de EPROM

Antes de grabar una EPROM es imprescindible asegurarse de que está completamente vacía. Las EPROM con ventana de cuarzo, como la **AM27C1024**, se borran mediante exposición a luz ultravioleta (UV).

**Procedimiento**

1. Retira cualquier etiqueta que cubra la ventana de cuarzo de la EPROM y limpia la superficie.

{% hint style="info" %}
Comprueba que no hay daños físicos en el chip, la ventana o las patillas, en tal caso esa EPROM queda descartada.
{% endhint %}

2.  Introduce la EPROM en el borrador UV (EPROM Eraser) con la ventana orientada hacia la lámpara.<br>

    <figure><img src="../.gitbook/assets/imagen (70).png" alt="" width="375"><figcaption></figcaption></figure>


3.  Cierra el borrador para evitar la exposición accidental a la radiación UV.<br>

    <figure><img src="../.gitbook/assets/imagen (71).png" alt="" width="375"><figcaption></figcaption></figure>


4.  Programa un tiempo de borrado de entre **15 y 30 minutos** (consulta siempre la hoja de datos del fabricante), si son usadas, mejor 30.<br>

    <figure><img src="../.gitbook/assets/imagen (72).png" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="warning" %}
La radiación UV puede degradar gradualmente la EPROM con el paso de los años y múltiples ciclos de borrado. Por ello, se recomienda realizar únicamente el tiempo de exposición necesario y volver a cubrir la ventana con una etiqueta opaca una vez finalizada la grabación para evitar borrados accidentales por exposición prolongada a la luz solar o fuentes intensas de radiación UV.
{% endhint %}

5. Una vez finalizado el proceso, retira la EPROM del borrador.



#### 2. Verificación del borrado

Después del borrado, coloca la EPROM en el programador respetando la posición y ejecuta la función **Blank Check** desde Xgpro.

<figure><img src="../.gitbook/assets/imagen (74).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (75).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/imagen (42).png" alt=""><figcaption></figcaption></figure>

Una EPROM correctamente borrada debe devolver:

```bash
FF FF FF FF FF FF FF FF ...
```

en todas sus posiciones de memoria.

<figure><img src="../.gitbook/assets/imagen (38).png" alt=""><figcaption></figcaption></figure>

Si el **Blank Check** falla, vuelve a introducir la EPROM en el borrador UV ([Step 1](grabar-eprom.md#id-1.-borrado-de-eprom)) durante unos minutos adicionales (5/10 minutos) y repite la comprobación hasta que toda la memoria esté a `0xFF`.

#### 3. Grabar la EPROM

Una vez que la EPROM ha sido borrada correctamente y ha superado el **Blank Check**, se puede proceder a la grabación del contenido deseado.

**Procedimiento**

1. Inserta la EPROM en el zócalo ZIF del programador respetando la orientación indicada por el fabricante.\
   ![](<../.gitbook/assets/imagen (44).png>)
2. Inicia el software **Xgpro**.
3.  Selecciona el modelo exacto de la EPROM (en este ejemplo, **AM27C1024**).<br>

    <figure><img src="../.gitbook/assets/imagen (55).png" alt=""><figcaption></figcaption></figure>
4.  Carga el fichero binario (`.bin`, `.rom`, `.img`, etc.) que deseas grabar mediante la opción **File → Open**.<br>

    <figure><img src="../.gitbook/assets/imagen (58).png" alt=""><figcaption></figcaption></figure>
5.  Verifica que el tamaño del fichero es compatible con la capacidad de la EPROM.<br>

    <figure><img src="../.gitbook/assets/imagen (59).png" alt=""><figcaption></figcaption></figure>
6.  Comprueba que la configuración de voltajes y parámetros de programación es la adecuada para el dispositivo seleccionado. (por defecto el programa ya lo hace automáticamente)<br>

    <figure><img src="../.gitbook/assets/imagen (60).png" alt=""><figcaption></figcaption></figure>
7.  Pulsa **PROG.** para iniciar la grabación.<br>

    <figure><img src="../.gitbook/assets/imagen (64).png" alt=""><figcaption></figcaption></figure>



{% hint style="info" %}
**Recomendaciones**

* No extraigas la EPROM del programador durante la grabación.
* Evita interrupciones de alimentación eléctrica mientras el proceso está en curso.
* Utiliza siempre el modelo exacto de EPROM en Xgpro para garantizar que se emplean los voltajes y algoritmos de programación adecuados.
{% endhint %}

\
**Durante la grabación**

El programador escribirá el contenido en la EPROM y mostrará el progreso del proceso. Dependiendo del tamaño del dispositivo, la grabación puede tardar desde unos segundos hasta varios minutos.

<figure><img src="../.gitbook/assets/imagen (62).png" alt=""><figcaption></figcaption></figure>

\
Grabación finalizada con éxito ✅

<figure><img src="../.gitbook/assets/imagen (63).png" alt=""><figcaption></figcaption></figure>

#### 4. Verificación de la grabación

Una vez finalizado el proceso de programación, es obligatorio realizar una verificación para asegurar que los datos escritos en la EPROM coinciden exactamente con el fichero original.

**Procedimiento**

1. Mantén la EPROM correctamente insertada en el programador.
2.  En **Xgpro**, selecciona la opción **Verify**.<br>

    <figure><img src="../.gitbook/assets/imagen (66).png" alt=""><figcaption></figcaption></figure>
3. Carga de nuevo el fichero original utilizado en la programación (_en Load, selecciona el fichero_).
4.  Ejecuta el proceso de verificación.<br>

    <figure><img src="../.gitbook/assets/imagen (67).png" alt=""><figcaption></figcaption></figure>

El programador leerá el contenido completo de la EPROM y lo comparará byte a byte con el archivo cargado.

**Resultado esperado**

* **Verify Finished** → La grabación es correcta y los datos coinciden exactamente.
* **Verify Fail** → Existe al menos una discrepancia entre la EPROM y el archivo original.

\
**Verificación avanzada (recomendada)**

Para aumentar la seguridad del proceso, se puede realizar una verificación externa adicional:

1. Realizar un **read (dump)** completo de la EPROM desde Xgpro.
2. Guardar el contenido como un nuevo fichero.
3. Comparar el fichero leído con el original mediante hash MD5 / SHA:

\
Dump:<br>

<figure><img src="../.gitbook/assets/imagen (68).png" alt=""><figcaption></figcaption></figure>

\
Fichero original:<br>

<figure><img src="../.gitbook/assets/imagen (69).png" alt=""><figcaption></figcaption></figure>

#### 5. Pruebas

Una vez verificada la grabación y montada la EPROM en el sistema destino, se realizan las pruebas funcionales para confirmar que el contenido programado se ejecuta correctamente en condiciones reales.

**Objetivo**

Validar que la EPROM no solo contiene datos correctos, sino que estos son interpretados adecuadamente por el sistema donde se utiliza.

**Procedimiento**

1. Instalar la EPROM en el equipo o placa destino, asegurando correcta orientación y contacto en el zócalo.
2. Encender el sistema y observar el comportamiento de arranque.
3. Comprobar las funciones principales dependientes del contenido de la EPROM (firmware, tablas de datos, BIOS, lógica de control, etc.).
4. Realizar pruebas operativas completas según el propósito del sistema (arranque en frío, reinicios, ciclos de carga, etc.).

**Validaciones típicas**

* Arranque correcto del sistema sin errores.
* Ausencia de bloqueos, reinicios o comportamientos anómalos.
* Funcionamiento correcto de todas las funciones dependientes del contenido de la EPROM.
* Estabilidad durante operación prolongada.

**Diagnóstico en caso de fallo**

Si el sistema no funciona correctamente:

* Revisar nuevamente la verificación ([Step 4](grabar-eprom.md#id-4.-verificacion-de-la-grabacion)).
* Confirmar que el fichero programado corresponde a la versión correcta.
* Verificar contactos físicos del zócalo y estado de los pines.
* Comprobar compatibilidad eléctrica y lógica de la EPROM con la placa.
* Repetir el proceso completo si es necesario.
