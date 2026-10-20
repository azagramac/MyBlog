# Personalización y optimización con Linux

## 🔧 Undervolt del Intel Core i7-4700MQ en Debian 13

### Objetivo

Reducir ligeramente la tensión solicitada por la CPU del Lenovo ThinkPad T440p mediante `intel-undervolt`, manteniendo las frecuencias y el rendimiento originales siempre que el sistema permanezca estable.

La configuración documentada aplica un offset de aproximadamente **−70.31 mV a CPU Core**.&#x20;

### 1. Requisitos

* Lenovo ThinkPad T440p.
* Intel Core i7-4700MQ.
* Debian 13.
* Acceso de administrador mediante `sudo`.
* Secure Boot desactivado en esta instalación.
* Herramientas de compilación y Git.

### 2. Instalar dependencias

```bash
sudo apt update
sudo apt install -y git build-essential pkg-config msr-tools
```

### 3. Descargar el código fuente

```bash
cd ~
git clone https://github.com/kitsunyan/intel-undervolt.git
cd intel-undervolt
```

### 4. Compilar e instalar

```bash
./configure --enable-systemd
make
sudo make install
```

Comprobar la ubicación del ejecutable:

```bash
command -v intel-undervolt
```

### 5. Configurar el offset

Editar la configuración:

```bash
sudo vim /etc/intel-undervolt.conf
```

Ajustes relevantes:

```bash
enable yes
undervolt 0 'CPU' -70
undervolt 1 'GPU' -20
undervolt 2 'CPU Cache' -70
undervolt 3 'System Agent' 0
undervolt 4 'Analog I/O' 0
```

{% hint style="info" %}
No se recomienda subir mas de -20 la GPU, es muy sensible, quizá a -25 seria el limite max. En cuanto a la CPU/CPU Cache, mantener los mismos valores, empezar en -60 mV, aplicar y probar, e ir subiendo de 5 en 5.
{% endhint %}

### 6. Aplicar y comprobar

```bash
sudo intel-undervolt apply
sudo intel-undervolt read
```

Resultado esperado para CPU Core / CPU Cache:

```bash
CPU (0): -70.31 mV
```



### 7. Habilitar la aplicación automática

Comprobar si el instalador ha proporcionado una unidad systemd:

```bash
systemctl cat intel-undervolt.service
```

Si existe:

```bash
sudo systemctl daemon-reload
sudo systemctl enable intel-undervolt.service
sudo systemctl start intel-undervolt.service
```

Si no existe, revisar el resultado de la instalación y la documentación de la versión descargada antes de crear una unidad manual.

### 8. Verificar después del reinicio

```bash
sudo reboot
```

Tras iniciar sesión:

```bash
sudo intel-undervolt read
systemctl is-enabled intel-undervolt.service
systemctl status intel-undervolt.service --no-pager
```

El offset de CPU Core debe continuar en aproximadamente −70.31 mV. El servicio debe estar habilitado para el arranque.

### 9. Pruebas de estabilidad del undervolt

Después de configurar el offset de CPU Core a 70.31 mV, es necesario comprobar la estabilidad del procesador bajo carga. Un undervolt demasiado agresivo puede provocar errores de cálculo, bloqueos o reinicios inesperados, incluso cuando el sistema parece funcionar correctamente en reposo.

En este equipo se utiliza `stress-ng` para someter los ocho hilos lógicos del Intel Core i7-4700MQ a una prueba de carga durante 10 minutos.

#### 9.1. Instalar las herramientas de prueba

Instalar `stress-ng` y `lm-sensors`:

```bash
sudo apt update
sudo apt install -y stress-ng lm-sensors
```

Comprobar la versión instalada:

```bash
stress-ng --version
```

#### 9.2. Comprobar los sensores antes de la prueba

Ejecutar:

```bash
sensors
```

Identificar las lecturas de temperatura del procesador, normalmente bajo `coretemp`, y las revoluciones del ventilador si el equipo expone ese sensor.

En el ThinkPad T440p, las lecturas relevantes incluyen:

* `Package id 0`: temperatura del paquete del procesador.
* `Core 0`, `Core 1`, etc.: temperaturas individuales de los núcleos.
* `fan1`: velocidad del ventilador, si está disponible.

Registrar las temperaturas en reposo antes de empezar. Las lecturas de sensores que indiquen `0.0 °C` o valores físicamente imposibles no deben interpretarse como temperaturas reales.

#### 9.3. Ejecutar la prueba de carga

Ejecutar la siguiente orden:

```bash
stress-ng --cpu 8 --verify --timeout 10m
```

Parámetros utilizados:

| Parámetro       | Función                                                                             |
| --------------- | ----------------------------------------------------------------------------------- |
| `--cpu 8`       | Ejecuta ocho trabajadores de carga de CPU, uno por cada hilo lógico del procesador. |
| `--verify`      | Solicita la verificación de los resultados de los trabajadores compatibles.         |
| `--timeout 10m` | Limita la duración de la prueba a 10 minutos.                                       |

La prueba genera carga de CPU y verifica resultados cuando el método de estrés utilizado admite esa comprobación. No garantiza que se detecten todos los posibles errores de estabilidad.

**Importante:** la temperatura puede subir rápidamente. No dejar el equipo sin supervisión durante la prueba y detenerla si aparecen temperaturas excesivas, errores, bloqueos o comportamientos anormales.

#### 9.4. Monitorizar las temperaturas durante la prueba

Abrir una segunda terminal mientras `stress-ng` está ejecutándose:

```bash
watch -n 2 sensors
```

El comando actualiza las lecturas cada dos segundos.

Observar especialmente:

* La temperatura máxima alcanzada por el paquete y los núcleos.
* La velocidad del ventilador.
* Si las temperaturas se estabilizan o continúan aumentando.
* La aparición de avisos térmicos o síntomas de limitación de frecuencia.

Para salir de `watch`, pulsar `Ctrl + C`.

En las pruebas realizadas inicialmente en este ThinkPad T440p, la temperatura máxima observada fue de aproximadamente **79 °C**, con valores sostenidos alrededor de 76–79 °C y una velocidad del ventilador cercana a 3.800 RPM.

Estas cifras son resultados observados en esa prueba concreta, no valores garantizados para todas las condiciones ambientales o cargas de trabajo.

#### 9.5. Interpretar el resultado de `stress-ng`

Una ejecución satisfactoria debería terminar mostrando un resumen equivalente a:

```bash
passed: 8: cpu (8)
failed: 0
skipped: 0
metrics untrustworthy: 0
successful run completed in 10 mins
```

Interpretación:

* `passed: 8`: los ocho trabajadores de CPU terminaron satisfactoriamente.
* `failed: 0`: no se registraron fallos en los trabajadores.
* `skipped: 0`: no se omitieron pruebas.
* `successful run completed in 10 mins`: la ejecución terminó correctamente tras el tiempo previsto.

El texto exacto puede variar según la versión de `stress-ng`.

#### 9.6. Comprobar errores del kernel después de la prueba

Consultar los mensajes del kernel del arranque actual:

```bash
sudo journalctl -k -b --no-pager \
  | grep -Ei 'mce|machine check|hardware error|EDAC'
```

Un mensaje como:

```bash
EDAC MC: Ver: 3.0.0
```

es informativo y, por sí solo, no indica un error de hardware.

También se pueden buscar errores de CPU y avisos térmicos:

```bash
sudo journalctl -k -b --no-pager \
  | grep -Ei 'mce|machine check|hardware error|thermal|throttl'
```

La ausencia de mensajes coincidentes no demuestra por sí sola la estabilidad absoluta del undervolt, pero ayuda a detectar algunos problemas.

#### 9.7. Comprobar el throttling térmico

Consultar los contadores del procesador lógico CPU 0:

```bash
for f in /sys/devices/system/cpu/cpu0/thermal_throttle/*; do
    printf '%s: ' "${f##*/}"
    cat "$f"
done
```

Los contadores `core_throttle_count` y `package_throttle_count`, cuando están disponibles, permiten comprobar si el kernel ha registrado eventos de limitación térmica.

Un valor de cero indica que ese contador no ha registrado eventos; no descarta todas las formas de limitación de frecuencia ni otros límites de potencia.

#### 9.8. Criterios para considerar satisfactoria la prueba

La prueba inicial puede considerarse satisfactoria si se cumplen estas condiciones:

* `stress-ng` termina sin errores.
* No aparecen bloqueos, reinicios ni errores de cálculo observables.
* Las temperaturas permanecen dentro de límites aceptables para el procesador y la refrigeración.
* No se observan errores de hardware relevantes en los registros.
* El equipo continúa funcionando correctamente después de la prueba.

Una prueba de 10 minutos es una primera comprobación, no una certificación completa de estabilidad. Conviene continuar con uso cotidiano, aplicaciones exigentes, arranques en frío y ciclos de suspensión y reanudación.

#### 9.9. Resultados de la prueba inicial

En la primera prueba documentada de este ThinkPad T440p se obtuvieron los siguientes resultados:

| Métrica                                          | Resultado             |
| ------------------------------------------------ | --------------------- |
| Procesador                                       | Intel Core i7-4700MQ  |
| Offset de CPU Core                               | −70.31 mV             |
| Duración                                         | 10 minutos            |
| Trabajadores de CPU                              | 8                     |
| Trabajadores fallidos                            | 0                     |
| Temperatura máxima observada                     | Aproximadamente 79 °C |
| Throttling térmico en los contadores consultados | No registrado         |
| Errores MCE o hardware en la salida revisada     | No detectados         |

**Conclusión:** el undervolt de −70.31 mV superó la prueba inicial de carga de 10 minutos. Se mantiene este valor sin aumentar el offset hasta completar más pruebas de estabilidad.



## 🔋 Calibrar la batería del Lenovo ThinkPad T440p en Debian 13

### Objetivo

Realizar una recalibración de la batería mediante TLP en Debian 13, utilizando el controlador compatible con los ThinkPad para que el sistema gestione el ciclo de descarga y carga.

En este equipo se utiliza una batería principal `BAT0`, modelo **Lenovo 45N1147**, fabricada por LGC. TLP detecta que el controlador `natacpi` admite tanto los umbrales de carga como la recalibración.

La finalidad es mejorar la precisión del indicador de carga y de la capacidad estimada. **La calibración no recupera la capacidad física perdida por el desgaste de la batería.**

### 1. Comprobar los requisitos

Instalar TLP si todavía no está instalado:

```bash
sudo apt update
sudo apt install -y tlp
```

Comprobar su versión:

```bash
tlp --version
```

En esta instalación se utiliza **TLP 1.8.0**.

Consultar el estado de la batería:

```bash
sudo tlp-stat -b
```

### 2. Interpretar el estado inicial de la batería

El informe obtenido en el ThinkPad T440p muestra estos valores:

| Parámetro                             | Valor inicial          |
| ------------------------------------- | ---------------------- |
| Batería                               | BAT0 (Main / Internal) |
| Fabricante                            | LGC                    |
| Modelo                                | 45N1147                |
| Capacidad de diseño                   | 56.160 mWh             |
| Capacidad de carga completa estimada  | 45.770 mWh             |
| Energía indicada inicialmente         | 47.910 mWh             |
| Capacidad estimada respecto al diseño | 81,5 %                 |
| Estado                                | Charging               |
| Controlador                           | `natacpi`              |
| Recalibración compatible              | Sí                     |

La capacidad estimada respecto a la capacidad de diseño se calcula así:

```json
Capacidad estimada = (45.770 / 56.160) × 100 ≈ 81,5 %
```

Esto significa que la capacidad de carga completa estimada es aproximadamente el 81,5 % de la capacidad original de diseño.

#### ¿Por qué aparece una carga del 104,7 %?

TLP informa de una carga superior al 100 %. Este valor se obtiene al comparar la energía indicada en ese momento con la capacidad de carga completa estimada:

```json
Carga indicada respecto a la capacidad estimada = (47.910 / 45.770) × 100 ≈ 104,7 %
```

Es una discrepancia entre las lecturas del indicador y la capacidad estimada. Puede justificar una recalibración, pero no demuestra por sí sola que el medidor esté descalibrado ni permite determinar el estado físico exacto de la batería.

El contador de ciclos aparece como `0 (or not supported)`, por lo que no debe interpretarse como prueba de que la batería no haya tenido ciclos de uso.

### 3. Confirmar la compatibilidad con TLP

En el informe de esta instalación aparecen las siguientes características:

```bash
Battery Care
Plugin: thinkpad
Supported features: charge thresholds, recalibration
Driver usage:
* natacpi (thinkpad_acpi) = active (charge thresholds, recalibration)
```

Esto confirma que TLP reconoce el soporte de recalibración mediante el controlador activo.

También aparecen estos umbrales:

```bash
START_CHARGE_THRESH_BAT0/1: 0(off)..96(default)..99
STOP_CHARGE_THRESH_BAT0/1: 1..100(default)
```

Los valores concretos configurados para `BAT0` deben comprobarse en el informe completo. En la lectura inicial, los umbrales expuestos por el sistema son 0 % y 100 %.

### 4. Preparar el equipo

Antes de iniciar el proceso:

1. Conectar el cargador de corriente.
2. Guardar los documentos y cerrar las aplicaciones que no sean necesarias.
3. Colocar el portátil sobre una superficie estable y ventilada.
4. Evitar que el equipo entre en suspensión durante el procedimiento.
5. No iniciar la calibración si hay indicios de batería hinchada, daños físicos o calentamiento anormal.

La recalibración puede tardar varias horas. Conviene mantener el equipo supervisado y disponer de una copia de los trabajos importantes.

### 5. Iniciar la recalibración automática

Ejecutar:

```bash
sudo tlp recalibrate BAT0
```

`BAT0` identifica la batería principal detectada por TLP en este equipo.

No es necesario realizar manualmente una descarga completa mediante el agotamiento de la batería: TLP utiliza el soporte de recalibración del controlador para gestionar el proceso.

### 6. Interpretar la salida del proceso

En la primera ejecución de este equipo, TLP mostró:

```bash
Currently discharging battery BAT0 to 0% (0 mWh):
voltage = 11557 [mV]
remaining energy = 37820 [mWh]
remaining percent = 82.6 [%]
remaining time = 189 [min]
power = 11961 [mW]
status = Discharging
force-discharge = 1
Press Ctrl+C to cancel
```

Interpretación:

| Parámetro           | Valor       | Significado                                      |
| ------------------- | ----------- | ------------------------------------------------ |
| `remaining energy`  | 37.820 mWh  | Energía restante estimada al inicio de esta fase |
| `remaining percent` | 82,6 %      | Porcentaje informado por el controlador          |
| `remaining time`    | 189 min     | Estimación inicial del tiempo restante           |
| `power`             | 11.961 mW   | Potencia de descarga indicada                    |
| `status`            | Discharging | La batería se está descargando                   |
| `force-discharge`   | 1           | Está activa la descarga forzada                  |

La descarga forzada permite que la batería se descargue aunque el adaptador de corriente siga conectado.

La estimación de 189 minutos no es una duración garantizada: puede variar según el consumo del portátil, las condiciones de la batería y las actualizaciones del controlador.

#### Durante la descarga

* Mantener el cargador conectado.
* No pulsar `Ctrl+C`, porque cancela el proceso.
* Mantener abierta la terminal donde se está ejecutando TLP.
* Evitar la suspensión automática del equipo.
* Vigilar la temperatura y cualquier comportamiento anormal.

Es posible realizar tareas ligeras, pero es preferible evitar cargas innecesarias y dejar el equipo en condiciones estables.

### 7. Esperar a que finalice el ciclo

La orden de recalibración puede gestionar varias fases del procedimiento según el controlador:

1. Descarga controlada de la batería.
2. Transición a la fase de carga que corresponda.
3. Finalización del procedimiento de recalibración.

El proceso debe dejarse terminar y hay que seguir los mensajes que muestre TLP. No se debe asumir que la descarga ha concluido simplemente porque el indicador de batería marque 0 %.

Si el proceso se interrumpe, aparece un error o el equipo corre el riesgo de apagarse de forma inesperada, revisar el estado del controlador y la documentación de TLP antes de repetirlo.

### 8. Comprobar el estado después de la recalibración

Cuando TLP termine, consultar de nuevo el informe:

```bash
sudo tlp-stat -b
```

Registrar los nuevos valores de:

* `energy_full_design`
* `energy_full`
* `energy_now`
* `capacity`
* `status`
* Los umbrales de carga, si aparecen.

Comparar los resultados con las lecturas iniciales de este artículo.

También se puede consultar el estado directamente desde el subsistema de energía:

```bash
cat /sys/class/power_supply/BAT0/uevent
```

Este archivo muestra los datos que el kernel expone para `BAT0`.

#### ¿Cómo interpretar los resultados?

* Si las lecturas del porcentaje y de la energía son más coherentes, la recalibración puede haber mejorado la estimación del medidor.
* Si `energy_full` permanece alrededor de 45.770 mWh, la capacidad estimada sigue siendo aproximadamente el 81,5 % de la capacidad de diseño.
* Si el indicador sigue mostrando valores anómalos, no hay que repetir ciclos completos de manera rutinaria: primero conviene revisar el firmware, las lecturas de la batería y su estado físico.
* Si la autonomía real sigue siendo reducida, la recalibración no podrá recuperar la capacidad química perdida.

### 9. Resultados iniciales y estado de la operación

La primera lectura identificó una capacidad estimada del 81,5 % y un porcentaje de carga calculado del 104,7 %. TLP confirmó que el controlador admite recalibración y comenzó una descarga forzada.

La salida inicial mostró un 82,6 % restante, una potencia de descarga de 11.961 mW y una estimación de 189 minutos para esa fase.

**La recalibración no debe considerarse completada hasta que TLP termine y se haya realizado una nueva lectura de `tlp-stat -b`.** Los valores posteriores permitirán documentar si cambió la estimación de capacidad y si el indicador de carga se normalizó.



## 📶 Sierra Wireless EM7345 con Simyo en Debian 13 KDE Plasma

### Objetivo

Configurar el módem WWAN Sierra Wireless EM7345 del Lenovo ThinkPad T440p para conectarse a la red móvil de Simyo mediante Debian 13 y KDE Plasma.

La guía cubre la configuración de MBIM, la detección del dispositivo y la creación de la conexión móvil.

### 1. Requisitos

* Lenovo ThinkPad T440p.
* Módem Sierra Wireless EM7345 compatible con el equipo.
* Tarjeta SIM de Simyo activa y con servicio de datos.
* Debian 13 con KDE Plasma.
* NetworkManager y ModemManager instalados.

Comprobar que están disponibles las herramientas:

```bash
mmcli --version
nmcli --version
```

Si falta alguna, instalar los paquetes necesarios:

```bash
sudo apt update
sudo apt install -y modemmanager network-manager
```

### 2. Configurar el modo MBIM

Crear un archivo para indicar al controlador `cdc_ncm` que prefiera MBIM:

```bash
sudo tee /etc/modprobe.d/cdc_ncm.conf > /dev/null <<'EOF'
options cdc_ncm prefer_mbim=Y
EOF
```

Esta opción solicita al controlador que prefiera MBIM cuando sea compatible. No garantiza por sí sola la eliminación de todas las desconexiones.

**Nota:** si el módulo ya está cargado, el parámetro puede no aplicarse hasta volver a cargarlo o reiniciar. Evitar descargar controladores en uso.

### 3. Reiniciar ModemManager

```bash
sudo systemctl restart ModemManager
```

Comprobar su estado:

```bash
systemctl status ModemManager --no-pager
```

### 4. Verificar la detección del módem

```bash
mmcli -L
```

La salida debería incluir un dispositivo similar a:

```bash
/org/freedesktop/ModemManager1/Modem/0 [Sierra Wireless] EM7345
```

El número del módem puede variar.

Si no aparece, comprobar la conexión física de la tarjeta, su compatibilidad con la BIOS, los mensajes del kernel y los registros de ModemManager.

### 5. Consultar el estado del módem

Si el dispositivo aparece como módem 0:

```bash
mmcli -m 0
```

Si se ha asignado otro número, sustituir el `0` por el identificador correspondiente.

Este comando permite revisar el estado, el fabricante, el modelo, las capacidades de red y la información de SIM que exponga el dispositivo.

### 6. Crear la conexión móvil en KDE Plasma

1. Abrir el menú de Redes desde la bandeja del sistema.
2. Entrar en la configuración de conexiones.
3. Añadir una conexión nueva de banda ancha móvil.
4. Seleccionar España.
5. Buscar el operador deseado en la lista de operadores.
6. Si aparece, seleccionar el operador y comprobar los parámetros generados.
7. Si no aparece, configurar el APN manualmente con los datos oficiales del operador.
8. Guardar la conexión y seleccionar la SIM correspondiente.

#### APN

Ejemplo de configuración con Simyo:

```bash
APN: orangeworld
Usuario: vacío
Contraseña: vacía
```

{% hint style="info" %}
**Importante:** verificar el APN antes de aplicar esta configuración. Los perfiles de operador pueden cambiar y un APN antiguo puede impedir la conexión.
{% endhint %}

### 7. Comprobar la conexión

Una vez guardada la conexión, habilitar la banda ancha móvil desde KDE Plasma e intentar conectarse.

Desde la terminal, se puede consultar el estado general de NetworkManager:

```bash
nmcli general status
nmcli connection show
nmcli device status
```

El módem debe aparecer como dispositivo móvil y la conexión debe quedar activa cuando se conecte correctamente.

### 8. Diagnóstico de problemas

Consultar el registro de ModemManager:

```bash
sudo journalctl -u ModemManager -b --no-pager
```

Consultar los mensajes del kernel relacionados con el módem:

```bash
sudo journalctl -k -b --no-pager | grep -Ei 'mbim|cdc_ncm|wwan|usb'
```

Si no se establece la conexión, comprobar:

* Que la SIM esté activa y no tenga un PIN pendiente.
* Que la tarjeta tenga cobertura móvil.
* Que el APN sea correcto.
* Que el módem esté desbloqueado y permitido por la configuración del equipo.
* Que el firmware y los controladores sean compatibles con Debian 13.

### 9. Comportamiento con Wi-Fi

La prioridad entre Wi-Fi y banda ancha móvil depende de las métricas y rutas de NetworkManager. No debe darse por hecho que la conexión móvil permanecerá siempre en reposo ni que cambiará automáticamente al perder el Wi-Fi.

Para comprobar las rutas activas:

```bash
ip route
nmcli connection show --active
```

Los SMS también dependen de las capacidades del módem, la SIM, ModemManager y la aplicación de notificaciones instalada. No se garantiza que KDE muestre automáticamente una notificación SMS sin una aplicación compatible.

###
