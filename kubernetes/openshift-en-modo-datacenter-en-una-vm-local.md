# OpenShift en modo Datacenter en una VM local

La instalación de un cluster de openshift, no es compleja, pero si entretenida, tendremos que tener recursos de hardware suficientes para poder montarlo en nuestro equipo en una VM que vamos a crear. Mencionar que solo tienes 60 días de uso desde que la creas, la uses o no, 60 dias maximo. \


**Requisitos de dev o pre con cargas de trabajo:**\
\- CPU: minimo 9 cores, recomendable 10 o superior.\
\- RAM: 16Gb, aunque dependiendo lo que tengas corriendo, subir a 32/64Gb\
\- Discos: 2 discos, principal para OS de mínimo 120Gb, secundario para storage, 400Gb o superior.\
\- La VM tiene que salir a internet, necesitas tener abierto los puertos 443 y 6443 TCP.&#x20;

Para esta prueba es una VM mas sencilla, pero no va ejecutar cargas de trabajo. \
\
Iniciar sesión en [https://console.redhat.com/](https://console.redhat.com/)

<figure><img src="../.gitbook/assets/Captura desde 2024-12-02 16-54-38.png" alt=""><figcaption></figcaption></figure>

Menú principal, seleccionamos "**Red Hat OpenShift**"

<figure><img src="../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

En el menú lateral, seleccionamos "**Cluster List**"

<figure><img src="../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

Crearemos nuestro primer cluster, pulsando en "**Create cluster**"

<figure><img src="../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

Como el cluster que vamos a crear, va ser en nuestro entorno local, seleccionaremos la pestaña "**Datacenter**", y le damos "**Create cluster**"

<figure><img src="../.gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>

Ahora solo queda rellenar el formulario.

<figure><img src="../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Muy importante la versión de Openshift que quieras desplegar
{% endhint %}



Para este ejemplo, se va desplegar el cluster de tipo "**Single node**", rellenamos y pulsamos "Next"

<figure><img src="../.gitbook/assets/image (111).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Un clúster de un solo nodo "Single Node" en OpenShift consta de un solo nodo o host que está configurado para ejecutar cargas de trabajo.
{% endhint %}



Ahora marcamos los operadores que necesitemos y pulsamos "Next", para este ejemplo se va usar "[Logical Volume Manager Storage](https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/8/html-single/configuring_and_managing_logical_volumes/index)".

<figure><img src="../.gitbook/assets/image (114).png" alt=""><figcaption></figcaption></figure>

Ahora vamos a generar la ISO con la que usaremos para instalar nuestro cluster.\
aquí solo debemos pulsar en "**Add host**" para que nos aparezca el asistente.&#x20;

<figure><img src="../.gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure>

Tendremos 3 opciones para generar la ISO.

* Full image (ISO mas grande, ya que contiene todo el sistema)
* Minimal Image (la instalación requiere de salida a internet)
* iPXE (usada para ser arrancada desde red)

<figure><img src="../.gitbook/assets/image (117).png" alt=""><figcaption></figcaption></figure>

Seleccionamos "**Full Image**"

Podremos aprovechar para copiar la clave pública SSH para poder conectarnos mas fácilmente a la maquina vía SSH, para ello deberemos generar una clave pública SSH.&#x20;

\
Para generar la clave si no la tenemos, escribimos en el terminal, aquí generamos una clave de tipo RSA de 4096 bits.&#x20;

```sh
ssh-keygen -t rsa -b 4096
```

```sh
Generating public/private rsa key pair.
Enter file in which to save the key (/home/$USER/.ssh/id_rsa_openshift): 
Created directory '/home/$USER/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/$USER/.ssh/id_rsa_openshift
Your public key has been saved in /home/$USER/.ssh/id_rsa_openshift.pub
```

y copiamos la clave pública que pegaremos en el formulario.

```sh
cat /home/$USER/.ssh/id_rsa_openshift.pub
```

Una vez tenemos rellenado los campos, pulsamos en "**Generate Discovery ISO**".&#x20;

Ahora podemos descargar la ISO, bien copiando el link que nos ofrece, o vía wget, una vez tenemos la iso descargada en local, podemos pulsar en "Close".&#x20;

<figure><img src="../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>

Ejemplo con wget

<figure><img src="../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

Ya tenemos nuestra ISO de OpenShift. ahora tenemos que montar la máquina donde lo vamos a desplegar.&#x20;



**Creamos nuestra VM, para el ejemplo usamos VirtualBox**

Rellenamos los campos y le indicamos la ISO que vamos a usar.&#x20;

<figure><img src="../.gitbook/assets/image (122).png" alt=""><figcaption></figcaption></figure>

Especificamos el hardware que vamos a destinar, en este ejemplo 24Gb de RAM y 10 cores.&#x20;

{% hint style="info" %}
Nunca apures a la hora de reservar hardware en una VM, ya que el host necesita recursos, recomendable no sobrepasar el 75% del hardware disponible.
{% endhint %}

<figure><img src="../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

Ahora vamos a tocar algunos ajustes.

<figure><img src="../.gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

Tenemos que añadir otro disco a nuestra VM y cambiar el tipo de red.&#x20;

Añadiremos otro disco.

<figure><img src="../.gitbook/assets/Captura desde 2024-12-02 17-51-38.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

y cambiamos el tipo de red y guardamos los cambios.

<figure><img src="../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

y ya tenemos la VM preparada, le damos a Iniciar.&#x20;

<figure><img src="../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

De momento, el terminal o ventana de nuestra VM lo podemos ignorar, nos vamos al dashboard de RedHat, y vemos que ya nos aparece nuestro cluster, pero hay que instalarlo.&#x20;

En caso de que no aparezca, revisa la red de la VM, seguramente no tenga salida a internet.

Aqui solo pulsamos en "Next"

<figure><img src="../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

Aqui podemos revisar la red, y configurar algunos parametros.&#x20;

<figure><img src="../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

Si esta todo ok, le damos a "Next"

<figure><img src="../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

Aquí veremos un resumen, revisamos todo bien. Si esta todo correcto, ahora si, podemos instalar el cluster, pulsando en "Install cluster"

<figure><img src="../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

Y comienza la instalación...

<figure><img src="../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Cuando lleve un 60% aprox, reiniciara la maquina, acuérdate de retirar la ISO para que no arranque desde ella de nuevo. Si se te pasa no te preocupes, la reinicias manualmente desde el menú de la VM, y retira la ISO.
{% endhint %}

<figure><img src="../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

Cuando reiniciemos la VM sin la ISO de arranque, continuará la instalación automáticamente.&#x20;

La instalación dura unos 30 minutos aprox, depende del hardware que tengas, los recursos destinados a la VM.

<figure><img src="../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

Detalle de la VM durante la instalacion, el proceso es automatico. &#x20;

<figure><img src="../.gitbook/assets/Captura desde 2024-12-02 18-21-29.png" alt=""><figcaption></figcaption></figure>

Instalación completada. ✅ _(aunque el proceso final requiere algo mas de tiempo, el tiempo total fue de casi 1h, poco mas de 33 minutos solo para la instalacion, y el resto pasos post instalacion.)_.\
Tendremos disponible para descargar el fichero KUBECONFIG para poder conectarnos a nuestro cluster via terminal con [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) y tendremos el endpoint generado para acceder vía web.&#x20;

<figure><img src="../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

Despues de reiniciar la VM al finalizar la instalación, podremos acceder al endopoint.&#x20;

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

Hasta pronto!
