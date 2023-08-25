# Comprobar vulnerabilidades de imagenes docker

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Instalacion del ejecutable

```sh
curl --compressed https://static.snyk.io/cli/latest/snyk-linux -o snyk
chmod +x snyk
mv snyk /usr/local/bin/
```

Cambiamos la URL por la plataforma que vamos a usar

* **Linux**: [https://static.snyk.io/cli/latest/snyk-linux](https://static.snyk.io/cli/latest/snyk-linux)
* **Linux/arm64**: [https://static.snyk.io/cli/latest/snyk-linux-arm64](https://static.snyk.io/cli/latest/snyk-linux-arm64)
* **Alpine**: [https://static.snyk.io/cli/latest/snyk-alpine](https://static.snyk.io/cli/latest/snyk-alpine)
* **macOS**: [https://static.snyk.io/cli/latest/snyk-macos](https://static.snyk.io/cli/latest/snyk-macos)

## Instalacion con hombrew (macOS)

```bash
brew tap snyk/tap
brew install snyk
```

## Instalacion con Scoop (Windows)

```powershell
scoop bucket add snyk https://github.com/snyk/scoop-snyk
scoop install snyk
```

\
En Linux, se necesita ademas un paquete adicional.

```sh
sudo apt install xdg-utils -y
```



Nos registramos en [snyk.io](https://snyk.io) y despues de configurar nuestra cuenta, podremos hacer login

```sh
snyk auth
```

Al ejecutarlo nos genera un link para el Auth

```sh
# snyk auth

Now redirecting you to our auth page, go ahead and log in,
and once the auth is complete, return to this prompt and you'll
be ready to start using snyk.

If you can't wait use this url:
https://app.snyk.io/login?token=XXXXXXXXXXXXXXXXXXXXXXXXXXXX&utm_medium=cli&utm_source=cli&utm_campaign=CLI_V1_PLUGIN&utm_campaign_content=1.1196.0&os=linux&docker=false

Your account has been authenticated. Snyk is now ready to be used.
```

Entramos en la URL y pinchamos en el boton

<figure><img src="../.gitbook/assets/Captura desde 2023-07-28 14-40-35.png" alt=""><figcaption></figcaption></figure>

y ahora ya podremos hacer un test a nuestra imagen

```sh
snyk container test organizacion/imagen:latest
```

Ejemplo:

```sh
snyk container test azagramac/adguard:latest

Testing adguard/adguard:latest...

✗ Low severity vulnerability found in openssl/libcrypto3
  Description: CVE-2023-3446
  Info: https://security.snyk.io/vuln/SNYK-ALPINE318-OPENSSL-5788370
  Introduced through: openssl/libcrypto3@3.1.1-r1, apk-tools/apk-tools@2.14.0-r2, busybox/ssl_client@1.36.1-r0, ca-certificates/ca-certificates@20230506-r0, openssl/libssl3@3.1.1-r1
  From: openssl/libcrypto3@3.1.1-r1
  From: apk-tools/apk-tools@2.14.0-r2 > openssl/libcrypto3@3.1.1-r1
  From: busybox/ssl_client@1.36.1-r0 > openssl/libcrypto3@3.1.1-r1
  and 5 more...
  Image layer: 'apk --no-cache add ca-certificates libcap tzdata'
  Fixed in: 3.1.1-r3

✗ Medium severity vulnerability found in openssl/libcrypto3
  Description: Improper Authentication
  Info: https://security.snyk.io/vuln/SNYK-ALPINE318-OPENSSL-5776808
  Introduced through: openssl/libcrypto3@3.1.1-r1, apk-tools/apk-tools@2.14.0-r2, busybox/ssl_client@1.36.1-r0, ca-certificates/ca-certificates@20230506-r0, openssl/libssl3@3.1.1-r1
  From: openssl/libcrypto3@3.1.1-r1
  From: apk-tools/apk-tools@2.14.0-r2 > openssl/libcrypto3@3.1.1-r1
  From: busybox/ssl_client@1.36.1-r0 > openssl/libcrypto3@3.1.1-r1
  and 5 more...
  Image layer: 'apk --no-cache add ca-certificates libcap tzdata'
  Fixed in: 3.1.1-r2



Organization:      adguard
Package manager:   apk
Project name:      docker-image|adguard/adguard
Docker image:      adguard/adguard:latest
Platform:          linux/arm64
Licenses:          enabled

Tested 20 dependencies for known issues, found 2 issues.

Snyk wasn’t able to auto detect the base image, use `--file` option to get base image remediation advice.
Example: $ snyk container test adguard/adguard:latest --file=path/to/Dockerfile

To remove this message in the future, please run `snyk config set disableSuggestions=true`

-------------------------------------------------------

Testing adguard/adguard:latest...

Organization:      adguard
Package manager:   gomodules
Target file:       /opt/adguardhome/AdGuardHome
Project name:      github.com/AdguardTeam/AdGuardHome
Docker image:      adguard/adguard:latest
Licenses:          enabled

✔ Tested 121 dependencies for known issues, no vulnerable paths found.


Tested 2 projects, 1 contained vulnerable paths.
```

Pero aqui no acaba la cosa, tenemos un dashboard via web

Entramos [https://app.snyk.io/](https://app.snyk.io/) con nuestras credenciales, y aqui podemos enlazar diferentes servicios y gestionar las imagenes.&#x20;
