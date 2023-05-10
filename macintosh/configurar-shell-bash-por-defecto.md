# Configurar shell bash por defecto

Listamos las shell disponibles.

```sh
cat /etc/shells
```

Nos devolverá las opciones disponibles:

```sh
MacBook-Pro:~ jose$ cat /etc/shells
# List of acceptable shells for chpass(1).
# Ftpd will not allow users to connect who are not using
# one of these shells.

/bin/bash
/bin/csh
/bin/dash
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zshshell
```

Establecemos por defecto la deseada, en nuestro caso, bash, nos pedirá nuestra contraseña de nuestro usuario.&#x20;

```sh
chsh -s /bin/bash
```

Cerramos la terminal y la volvemos abrir, si queremos asegurarnos de que estamos usando la shell correcta.

```sh
echo "$SHELL"
```

