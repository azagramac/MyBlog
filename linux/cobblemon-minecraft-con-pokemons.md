# Cobblemon, Minecraft con Pokemons

<figure><img src="../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

**Descargamos el instalador de** [**NeoForge**](https://neoforged.net/) \
\
para la versión de Minecracft que queremos usar, la 1.21.1 y la ultima stable de NeoForge, la version Fabric me ha dado muchos problemas de rendimiento.

<figure><img src="../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

**Descargar** [**Cabblemon**](https://modrinth.com/mod/cobblemon)**, version NeoForge**

Importante, la version de Minecraft soportada actualmente es la 1.21.1, la ultima es la 26.3, esa no sirve. Descargamos tambien las dependencias.

<figure><img src="../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

\
Necesitamos tener Java instalado, bien la version OpenJDK o la version de Oracle JDK

```shellscript
$ java -version
java version "21.0.11" 2026-04-21 LTS
Java(TM) SE Runtime Environment (build 21.0.11+9-LTS-211)
Java HotSpot(TM) 64-Bit Server VM (build 21.0.11+9-LTS-211, mixed mode, sharing)
```

Con los artefactos .jar descargados, instalamos NeoForge

```shellscript
$ java -jar neoforge-21.1.251-installer.jar
```

y nos saldrá la ventana del instalador.&#x20;

<figure><img src="../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

Seleccionamos Install Client si vamos a jugar en local, en nuestro equipo, el sistema detectara la instalación de minecraft y le damos a "**Proceed**".

Una vez instalado que tarda muy poco, ya tenemos la nueva versión instalada.

Copiamos el .jar de Cobblemon en la ruta `/home/$USER/.minecraft/mods/`

```shellscript
~/.minecraft/mods$ ls -l
total 142912
-rw-rw-r-- 1 jose jose 139056524 sep 25 19:47 Cobblemon-neoforge-1.8.1+1.21.1.jar
-rw-rw-r-- 1 jose jose   7279264 sep 25 19:47 kotlinforforge-5.12.0-all.jar
```



Y ahora ya podemos abrir Minecraft

<figure><img src="../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

Antes de nada vamos hacer unos ajustes, pinchamos en los 3 "..." de NeoForge y le damos a "**Editar**"

<figure><img src="../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

Le indicamos el ejecutable de Java, en mi caso: `/lib/jvm/default-java/bin/java`

Y los argumentos, es un mod pesado.&#x20;

```shellscript
-Xms12G -Xmx16G -XX:+UnlockExperimentalVMOptions -XX:+UseG1GC -XX:MaxGCPauseMillis=50 -XX:G1NewSizePercent=20 -XX:G1ReservePercent=20 -XX:G1HeapRegionSize=16M -XX:+DisableExplicitGC
```

Guardamos y reiniciamos Minecraft y ahora si podemos jugar a Minecraft con Pokemons
