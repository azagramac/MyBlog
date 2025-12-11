# Generar claves GPG en la Yubikey

<figure><img src="../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

Vamos a generar una clave GPG en la propia llave con copia de seguridad de la clave privada en nuestro equipo.&#x20;



Verificamos que el sistema reconoce la llave

```bash
gpg --card-status
```

output:

```bash
Reader ...........: Yubico YubiKey OTP FIDO CCID 00 00
Application ID ...: D*******************************
Application type .: OpenPGP
Version ..........: 3.4
Manufacturer .....: Yubico
Serial number ....: ********
Name of cardholder: [no establecido]
Language prefs ...: [no establecido]
Salutation .......: 
URL of public key : [no establecido]
Login data .......: [no establecido]
Signature PIN ....: no forzado
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 0
KDF setting ......: off
UIF setting ......: Sign=off Decrypt=off Auth=off
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]
```

✅  🎉

Si tienes esta salida, continuamos.&#x20;



Ahora vamos a "entrar" en la llave para crear la clave GPG y algunos ajustes mas...

```bash
gpg --card-edit
```

Ahora nos saldrá un prompt de GPG, desde el cual vamos a usar:

```bash
gpg/card>
```



Entramos como administrador, desde este momento eres `root` en la Yubikey

```bash
gpg/card> admin
Se permiten órdenes de administrador
```



Lo primero cambiamos UIF que por defecto es: `Sign=off Decrypt=off Auth=off`

```bash
gpg/card> uif
usage: uif N [on|off|permanent]
       1 <= N <= 3
```

La forma de cambiar, es `{comando} {posicion} {estado}`



Marcamos como "**on**" las posiciones, Sig, Encrypt y Auth.&#x20;

```bash
gpg/card> uif 1 on # Signature
gpg/card> uif 2 on # Encryption
gpg/card> uif 3 on # Authentication
gpg/card> quit
```

Comprobamos el cambio:

```bash
gpg --card-status
```

output:

```bash
Reader ...........: Yubico YubiKey OTP FIDO CCID 00 00
Application ID ...: D*******************************
Application type .: OpenPGP
Version ..........: 3.4
Manufacturer .....: Yubico
Serial number ....: ********
Name of cardholder: [no establecido]
Language prefs ...: [no establecido]
Salutation .......: 
URL of public key : [no establecido]
Login data .......: [no establecido]
Signature PIN ....: no forzado
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 0
KDF setting ......: off
UIF setting ......: Sign=on Decrypt=on Auth=on
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]
```



Verificamos que "**UIF setting**" ahora están los valores en "**on**".&#x20;

```bash
UIF setting ......: Sign=on Decrypt=on Auth=on
```

\
Entramos de nuevo en la llave.

```bash
gpg --card-edit
```

Entramos como administrador.

```bash
gpg/card> admin
Se permiten órdenes de administrador
```

<figure><img src="../.gitbook/assets/image (164).png" alt="" width="188"><figcaption></figcaption></figure>

🔐 **Generamos la clave GPG**



Lo primero que nos preguntará es si queremos copia de seguridad de la clave privada, se guardara en el sistema, si le decimos que no, la clave privada estara únicamente en la Yubikey.

```bash
gpg/card> generate
¿Hacer copia de seguridad externa a la tarjeta de clave de cifrado? (S/n)
```

{% hint style="info" %}
Nos pedirá el PIN de usuario, lo metemos, si no has cambiado el PIN, es `123456`
{% endhint %}

\
Lo siguiente es especificar el período de validez de la clave, selecciona la opción deseada.

```bash
Por favor, especifique el período de validez de la clave.
         0 = la clave nunca caduca
      <n>  = la clave caduca en n días
      <n>w = la clave caduca en n semanas
      <n>m = la clave caduca en n meses
      <n>y = la clave caduca en n años
¿Validez de la clave (0)? 0
¿Es correcto? (s/n) s
```

Nos realizará una serie de preguntas:

```bash
¿Es correcto? (s/n) s

GnuPG debe construir un ID de usuario para identificar su clave.

Nombre y apellidos: INTRODUCE TU NOMBRE
Dirección de correo electrónico: INTRODUCE TU MAIL
Comentario: INTRODUCIR COMENTARIO
Ha seleccionado este ID de usuario:
    "NOMBRE (COMENTARIO) <MAIL>"

¿Cambia (N)ombre, (C)omentario, (D)irección o (V)ale/(S)alir? V
```

Aqui tardara un poco, pero en unos segundos nos habrá generado la clave GPG.&#x20;

```bash
Es necesario generar muchos bytes aleatorios. Es una buena idea realizar
alguna otra tarea (trabajar en otra ventana/consola, mover el ratón, usar
la red y los discos) durante la generación de números primos. Esto da al
generador de números aleatorios mayor oportunidad de recoger suficiente
entropía.
gpg: NOTA: copia de seguridad de la clave guardada en '/home/$USER/.gnupg/sk_******************.gpg'
gpg: creado el directorio '/home/$USER/.gnupg/openpgp-revocs.d'
gpg: certificado de revocación guardado como '/home/$USER/.gnupg/openpgp-revocs.d/{{ ID DE LA CLAVE }}.rev'
claves pública y secreta creadas y firmadas.

pub   rsa2048 2025-12-02 [SC]
      {{ ID DE LA CLAVE }}
uid                      NOMBRE (COMENTARIO) <MAIL>
sub   rsa2048 2025-12-02 [A]
sub   rsa2048 2025-12-02 [E]
```

Ya tienes la clave GPG generada y la copia de seguridad en el sistema. ✅ 🎉



**Exportar la clave pública.** La clave que nos tenemos que fijar es la marcada como `[SC]`

```bash
gpg --export -a {{ ID DE LA CLAVE }} > yubikey_public.asc
```

Nos dejará el fichero en el directorio que nos encontremos, contiene la clave pública, que podemos usar en Github, etc...&#x20;



**Publicar la clave publica en** [keys.openpgp.org](https://keys.openpgp.org/)

```bash
gpg --send-keys {{ ID DE LA CLAVE }} --keyserver keys.openpgp.org
```

Instante después es nos llegará un mail, para verificar la publicación de la clave.&#x20;

Tan solo confirmar pinchando en el link facilitado.&#x20;

<figure><img src="../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

y si ademas queremos verificar el mail con la clave publica, nos llegara otro mail.

<figure><img src="../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

Ya tendremos nuestra clave pública disponible, podemos verificarla directamente entrando en la web [keys.openpgp.org](https://keys.openpgp.org/) y poniendo el id de la clave o mail

<figure><img src="../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

Ahora vamos a personalizarla.&#x20;

```bash
gpg --card-edit
```



Accedemos como admin

```bash
gpg/card> admin
Se permiten órdenes de administrador
```

\
Le asignamos un titular, nos preguntara apellido y nombre.

```bash
gpg/card> name
Apellido del titular de la tarjeta: Apellido1 Apellido2
Nombre del titular de la tarjeta: Nombre
```

\
Ahora la URL de la clave pública, nos preguntara por la URL, para ello debemos entrar en la web citada antes, y copia la dirección de la clave, que descargara un fichero `.asc`

```bash
gpg/card> url
URL de donde recuperar la clave pública: https://keys.openpgp.org/vks/v1/by-fingerprint/{{ ID DE LA CLAVE }}
```

\
Metemos el mail o login habitual que usemos con nuestra clave GPG

```bash
gpg/card> login
Datos de login (nombre de la cuenta): INTRODUCE tu nick o mail
```

\
y por ultimo el idioma, por defecto ingles.

```bash
gpg/card> lang
Preferencias de idioma: es
```

\
Salimos y comprobamos los cambios.

```bash
gpg/card> quit
gpg --card-status
```

output:

```bash
Reader ...........: Yubico YubiKey OTP FIDO CCID 00 00
Application ID ...: D*******************************
Application type .: OpenPGP
Version ..........: 3.4
Manufacturer .....: Yubico
Serial number ....: ********
Name of cardholder: ************************
Language prefs ...: es
Salutation .......: 
URL of public key : https://keys.openpgp.org/vks/v1/by-fingerprint/*************
Login data .......: ************************
Signature PIN ....: no forzado
Key attributes ...: rsa4096 rsa4096 rsa4096
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 5
KDF setting ......: off
UIF setting ......: Sign=on Decrypt=on Auth=on
Signature key ....: **** **** **** **** ****  **** **** **** **** ****
      created ....: 2025-12-02 13:43:28
Encryption key....: **** **** **** **** ****  **** **** **** **** ****
      created ....: 2025-12-02 13:43:28
Authentication key: **** **** **** **** ****  **** **** **** **** ****
      created ....: 2025-12-02 13:43:28
General key info..: pub  rsa2048/************* 2025-12-02 ************* (*********) <**************>
sec>  rsa2048/*************  creado: 2025-12-11  caduca: nunca     
                                num. tarjeta: 0006 ********
ssb>  rsa2048/*************  creado: 2025-12-11  caduca: nunca     
                                num. tarjeta: 0006 ********
ssb>  rsa2048/*************  creado: 2025-12-11  caduca: nunca     
                                num. tarjeta: 0006 ********

```

Nos fijamos que los campos se han cambiado:

```bash
Language prefs ...: es
Name of cardholder: ************************
Login data .......: ************************
URL of public key : https://keys.openpgp.org/vks/v1/by-fingerprint/*************
```

✅ 🎉
