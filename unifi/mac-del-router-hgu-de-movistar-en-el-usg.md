# MAC del router HGU de Movistar en el USG

En este caso, no es estrictamente necesario para este operador (Movistar/O2), pero no esta demas conocerlo si se nos presenta el caso.\
\
Para ello necesitmos la MAC de nuestro router que nos facilita el ISP, es una numeracion hexadecimal tipo XX:XX:XX:XX:XX:XX.\
\
Ahora entramos por SSH al USG, las credenciales las tenemos en UniFi Controller, en _Settings / System / Application Configuration / Device SSH Authentication_

```
ssh USER@IP_USG
```

ya estamos en el USG, lo primero es sacarla informacion que tiene, comprobar el dispositivo, por defecto el WAN es el eth0, pero mejor asegurarse.

```
show configuration
```

Nos mostrara toda la configuracion en formato json, bajamos hasta que veamos un bloque asi:

```
interfaces {
    ethernet eth0 {
        description WAN
        vif 6 {
            description WAN
            firewall {
                in {
                    ipv6-name WANv6_IN
                    name WAN_IN
                }
                local {
                    ipv6-name WANv6_LOCAL
                    name WAN_LOCAL
                }
                out {
                    ipv6-name WANv6_OUT
                    name WAN_OUT
                }
            }
            pppoe 2 {
                default-route none
                firewall {
                    in {
                        ipv6-name WANv6_IN
                        name WAN_IN
                    }
                    local {
                        ipv6-name WANv6_LOCAL
                        name WAN_LOCAL
                    }
                    out {
                        ipv6-name WANv6_OUT
                        name WAN_OUT
                    }
                }
                name-server none
                password ****************
                user-id adslppp@telefonicanetpa
            }
        }
    }
```

Nos interesa conocer el numero de dispositivo de la WAN, lo tenemos en la linea 2 del bloque anterior.

Editamos la configuracion, para ello entramos en modo configuracion:

```
configure
```

Nos informara la consola que estamos en modo edicion asi, ten cuidado con lo que tocas!!!:

```
user@ubnt:~$ configure
[edit]
```

Para añadir la MAC del router del ISP, escribimos, donde ethX, ponemos el dispositivo de nuestra WAN, por defecto eth0, en XX:XX:XX:XX:XX:XX, la MAC del router del ISP:

```
set interfaces ethernet ethX mac XX:XX:XX:XX:XX:XX
```

validamos los cambios:

```
commit
```

Guardamos los cambios:

```
save
```

```
user@ubnt# save
Saving configuration to '/config/config.boot'...
Done
[edit]
```

salimos y reiniciamos el USG, despues nos volvemos a conectar por SSH y comprobamos que se han aplicado correctamente.

```
user@ubnt:~$ show configuration

...

interfaces {
    ethernet eth0 {
        description WAN
        duplex auto
        mac XX:XX:XX:XX:XX:XX
        speed auto
        vif 6 {
            description WAN
            firewall {
                in {
                    ipv6-name WANv6_IN
                    name WAN_IN
                }
                local {
                    ipv6-name WANv6_LOCAL
                    name WAN_LOCAL
                }
                out {
                    ipv6-name WANv6_OUT
                    name WAN_OUT
                }
            }
            pppoe 2 {
                default-route none
                firewall {
                    in {
                        ipv6-name WANv6_IN
                        name WAN_IN
                    }
                    local {
                        ipv6-name WANv6_LOCAL
                        name WAN_LOCAL
                    }
                    out {
                        ipv6-name WANv6_OUT
                        name WAN_OUT
                    }
                }
                mtu 1492
                name-server none
                password ****************
                user-id adslppp@telefonicanetpa
            }
        }
    }
```

Si nos fijamos en el bloque, en la linea 9, ya tenemos la MAC de nuestro router ISP, esto es todo.:thumbsup:
