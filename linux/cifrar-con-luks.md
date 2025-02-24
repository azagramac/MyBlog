# Cifrar con LUKS

<figure><img src="../.gitbook/assets/imagen (45).png" alt=""><figcaption></figcaption></figure>

LUKS (del inglés Linux Unified Key Setup, Configuración de clave unificada de Linux), completamente de código abierto, opera a nivel de núcleo y emplea el subsistema device mapper a través del módulo dm-crypt.\
\
En este tutorial vamos a crear una partición cifrada con autenticación desde un fichero y desde contraseña, mas adelante quiero implementar la autenticación con el modulo TPM2.



Vamos a crear una partition con una longitud de clave de 512 bit y un hash de SHA-512.

Supongamos que tenemos un pendrive y queremos guardar en el información sensible, nos vale un disco duro externo también.&#x20;

Mi recomendación es crear al menos 2 particiones en el dispositivo externo, la primera pequeña, si puede ser de al menos 1Gb perfecto, si puedes mas, mejor, para ficheros temporales y en FAT32.&#x20;

{% hint style="warning" %}
No me guardes en esa partición la clave de la partición cifrada
{% endhint %}

y la otra partición con el tamaño restante del dispositivo, creamos una partición formateada en EXT4, esto nos da igual ahora mismo, ya que vamos crear el volumen cifrado y luego lo vamos a formatear. \
\
Aquí tengo mi HDD externo de 1Tb con 2 particiones, lo que comentaba, la primera de 32GB en FAT32, sin cifrado y la segunda con el resto de capacidad cifrada y formateada en EXT4.&#x20;

<figure><img src="../.gitbook/assets/imagen (46).png" alt=""><figcaption></figcaption></figure>

Para cifrar una partición y la autenticación con fichero, con una longitud de clave de 512 bit y un hash SHA-512, donde `/dev/sdaX` cambiarlo por la partición que os corresponda.&#x20;



{% hint style="info" %}
Estos pasos formatean el contenido del dispositivo, lo que tenga se va borrar y no sera recuperable.
{% endhint %}

```sh
sudo cryptsetup luksFormat --cipher=aes-xts-plain64 --key-size=512 --hash=sha512 --use-random /dev/sdaX
```

y en un momento tendremos la partición cifrada, pero aun no hemos terminado.&#x20;

\
Ahora vamos a darle formato, antes de nada debemos desbloquear la partición. Podemos poner el nombre que queramos.&#x20;

```sh
sudo cryptsetup luksOpen /dev/sdaX {nombre}
```



Y la formateamos, en este caso de tipo ext4

```sh
sudo mkfs.ext4 /dev/mapper/{nombre}
```



La volvemos a bloquear una vez terminado el formateo de la partición.&#x20;

```sh
sudo cryptsetup luksClose {nombre}
```



Y ya tenemos nuestro dispositivo cifrado y bien cifrado, ya puedes memorizar bien la clave, no la dejes junto con el dispositivo.&#x20;

Podemos ver la cabecera de LUKS de nuestro dispositivo con

```sh
sudo cryptsetup luksDump /dev/sdaX
```

\
Y nos devolverá algo similar:

```sh
LUKS header information
Version:       	2
Epoch:         	3
Metadata area: 	16384 [bytes]
Keyslots area: 	16744448 [bytes]
UUID:          	fe223394-1612-8c20-9a0b-ac6e202294a2
Label:         	(no label)
Subsystem:     	(no subsystem)
Flags:       	(no flags)

Data segments:
  0: crypt
	offset: 16777216 [bytes]
	length: (whole device)
	cipher: aes-xts-plain64
	sector: 512 [bytes]

Keyslots:
  0: luks2
	Key:        512 bits
	Priority:   normal
	Cipher:     aes-xts-plain64
	Cipher key: 512 bits
	PBKDF:      argon2id
	Time cost:  14
	Memory:     1048576
	Threads:    4
	Salt:       60 31 f1 b7 6a 6e 55 8c 45 45 a5 b5 ac 18 46 08 
	            64 92 1d 83 2a f4 e3 93 89 2f 26 7a 70 87 3d 0e 
	AF stripes: 4000
	AF hash:    sha512
	Area offset:32768 [bytes]
	Area length:258048 [bytes]
	Digest ID:  0
Tokens:
Digests:
  0: pbkdf2
	Hash:       sha512
	Iterations: 264791
	Salt:       ca ad 4c d3 6e ba 83 2f 4f 70 c7 0c 11 91 24 e7 
	            cf 73 a6 5a 7c 43 4c 37 28 3b 1b 57 5b c3 6d 6c 
	Digest:     e3 38 c7 ea 0a 58 14 c6 8e 24 ae 8f 45 57 d4 55 
	            34 79 ca 0b a6 b5 45 bc 83 ee 1e e9 6a f6 de c6 
	            62 40 d2 57 ae 71 e0 f6 29 3c e8 86 c9 31 1f b2 
	            ed 44 57 6d d5 94 e3 de 60 64 88 f2 f4 ab 34 bb
```



Nos interesa de esa cabecera los puntos:

```bash
Key:        512 bits
Cipher:     aes-xts-plain64
Hash:       sha512
```



Si conectamos nuestro disco externo, nos pedirá la contraseña para desbloquear la partición cifrada.&#x20;

<figure><img src="../.gitbook/assets/imagen (47).png" alt=""><figcaption></figcaption></figure>



Esto lo podemos aplicar para cifrar un disco antes de instalar el sistema operativo, lo que probado Fedora 41, Anaconda si detecta el cifrado LUKS si lo has cifrado previamente y puedes instalar el sistema operativo (marcando sin cifrar), en cada inicio luego te pide las password para continuar el arranque, cuidado con cifrar /boot y EFI. Un gallifante para Fedora por el buen trabajo de integración de Anaconda con LUKS. \
\
En Debian 12.9 no detecta LUKS, con lo que si esta cifrado instalará encima el sistema normalmente pisando lo que tenga el disco. Mal Debian, MAL!!!&#x20;
