# Sacar el .crt y .key de un .pfx

Si tenemos un certificado en formato .pfx, y lo que necesitamos es el .crt y la clave privada en .key, tenemos que separar dichos ficheros del .pfx

Para ello, solo necesitamos OpenSSL, y la clave PEM con la que se genero el .pfx\


![](../.gitbook/assets/img\_cert.png)

\
**Extraer la clave privada a un fichero .key:**

```
openssl pkcs12 -in TU_FICHERO.pfx -nocerts -out FICHERO_CLAVE_PRIVADA.key
```

nos pedirá nuestra clave PEM, la metemos, nos la pedirá un par de veces para confirmar y si ha ido todo correctamente, tendremos nuetro .key con la clave privada

**Extraer el certificado a un fichero .crt:**

```
openssl pkcs12 -in TU_FICHERO.pfx -clcerts -nokeys -out CERTIFICADO.crt
```

lo mismo de antes, nos pedirá la clave PEM, y tendremos el .crt que es nuestro certificado.

\
Pero que ocurre si lo que necesitamos es nuestra clave privada sin cifrar?

```
openssl rsa -in FICHERO_CLAVE_PRIVADA.key -out FICHERO_CLAVE_PRIVADA_SIN_CIFRAR.key
```

Nos volverá a pedir la clave PEM que nos pidio la primera vez que sacamos el .key

También podemos tener nuestra clave privada, en formato PEM.

```
openssl rsa -in FICHERO_CLAVE_PRIVADA.key -outform PEM -out FICHERO_CLAVE_PRIVADA_PEM.key
```
