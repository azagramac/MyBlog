# Instalar Pyenv

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Instalamos dependencias

```sh
brew install openssl readline sqlite3 xz zlib tcl-tk
```

Ahora instalamos pyenv&#x20;

```sh
brew install pyenv
```

Verificamos la version

```sh
pyenv --version
```

Instalamos version de python, podemos listar las opciones disponibles.

```sh
pyenv install --list
```

Ejemplo:

```sh
pyenv install 3.8.16
```

Establecer de forma global

```sh
pyenv global 3.8.16
```

Establecer de forma local

```sh
pyenv local 3.8.16
```

Listar las versiones de python instaladas, el \* marca la version seleccionada anteriormente.

```sh
$ pyenv versions
  system
* 3.8.16 (set by /Users/$user/.pyenv/version)
```

Añadimos al `.bash_profile`

```sh
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
```

Comprobamos que tenemos cargada la version elegida

```sh
$ python3 --version
Python 3.8.16
```
