# Desplegar K8S con Kubespray

\
Para desplegar un cluster de kubernetes y tambien para resetearlo en caso necesario, de forma automatizada usando Kubespray.

<figure><img src="../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

Lo primero sera obtener y configurar el entorno donde vamos a usar kubespray, para ello nos descargamos desde el repo oficial [https://github.com/kubernetes-sigs/kubespray/releases](https://github.com/kubernetes-sigs/kubespray/releases)

\
Con el kubespray ya instalado en la maquina desde donde desplegaremos el cluster, no tiene porque ser en el propio cluster!!! Lo que si tienen que tener es visibilidad entre maquinas, para ello deberás tener en el /etc/hosts las IPs de las maquinas que compongan el cluster y las claves ssh compartidas entre si, incluida la maquina que usaras para desplegar todo con kubespray. \


#### Configurar Kubespray

Lo primero sera definir los nombres de los nodos e IPs que compondrán el cluster que queremos desplegar, aquí pondremos los nombres de los nodos y sus ips, tantos como vayamos a configurar en el cluster, en este ejemplo son 3 maquinas.

```shell
declare -a IPS=(nodo1,192.168.1.1 nodo2,192.168.1.2 nodo3,192.168.1.3)
```

\
En el ejemplo, hemos definido 3 nodos, cada uno con su IP.\
\
Ahora entramos en el directorio de Kubespray, y dentro de el veremos un directorio de nombre “**inventory**”, entramos, veremos que hay 2 carpetas “**local**” y “**sample**”, hacemos una copia de “**sample**” y le ponemos el nombre que queramos, podremos tener tantos clusters configurados como queramos y organizados por entornos...&#x20;

```shell
cp -rf sample NOMBRE_DE_TU_CLUSTER
```

\
Ahora vamos a configurar el inventario para poder despegarlo, sustituiremos el “NOMBRE\_DE\_TU\_CLUSTER” por el que corresponda que tengas definido.

```shell
CONFIG_FILE=inventory/NOMBRE_DE_TU_CLUSTER/hosts.yaml python contrib/inventory_builder/inventory ${IPS[@]}
```

\
Editamos ahora el fichero .../kubespray/inventory/NOMBRE\_DE\_TU\_CLUSTER/hosts.yaml

Aquí debemos colocar los nodos, cuales serán los master y cuales los workers.

```yaml
all:
  hosts:
    nodo1:
      ansible_host: 192.168.1.1
      ip: 192.168.1.1
      access_ip: 192.168.1.1
    nodo2:
      ansible_host: 192.168.1.2
      ip: 192.168.1.2
      access_ip: 192.168.1.2
    nodo3:
      ansible_host: 192.168.1.3
      ip: 192.168.1.3
      access_ip: 192.168.1.3
  children:
    kube_control_plane:
      hosts:
        nodo1:
    kube_node:
      hosts:
        nodo2:
        nodo3:
    etcd:
      hosts:
        nodo1:
        nodo2:
        nodo3:
    k8s_cluster:
      children:
      kube_control_plane:
      kube_node:
    calico_rr:
      hosts: {}
```

En este fragmento de código, vemos que el “_nodo1_” corresponde a _kube\_control\_plane_, osea el master del cluster, aquí podremos poner los nodos que queramos que sean masters, en _kube\_node_ tenemos “_nodo2_” y “_nodo3_”, que harán de workers, en el apartado etcd, pondremos todos los nodos que tenga el cluster.

En el ejemplo, nuestro clúster seria:\
1 Master\
2 Workers

Ahora editamos un yaml para definir unos parámetros

Editamos el fichero .../kubespray/inventory/NOMBRE\_DE\_TU\_CLUSTER/group\_vars/k8s\_cluster/k8s-cluster.yml

Esta linea definiremos la versión del cluster que vamos a desplegar.\
Para consultar las versiones de K8S disponibles: [https://kubernetes.io/releases/](https://kubernetes.io/releases/)\
`kube_version: 1.23.12`\
\
Definiremos el nombre del cluster a nivel local\
`cluster_name: nombre_cluster.local`\
\
Este parámetro, nos autorenueva los certificados, lo cambiamos a **`true`** si queremos que los autorenueve, por defecto es false.\
`auto_renew_certificates: false`

Tenemos también la opción de hacerlo por calendario, por defecto seria el primer lunes de cada mes.\
Si queremos establecerlo a otra fecha usaremos este parámetro, esta es configuración por defecto.\
`auto_renew_certificates_systemd_calendar: "Mon *-*-1,2,3,4,5,6,7 03:{{ groups['kube_control_plane'].index(inventory_hostname) }}0:00"`

### &#x20;Preparación de maquinas

Todas las maquinas que compongan el cluster, deben tener una mínima configuración.\
\
Instalamos los siguientes paquetes en TODOS los nodos.

```shell
sudo apt install -y ntp ssh-server ssh-client wget curl ca-certificates vim
```

Debemos editar los /etc/hosts y añadir el hostname e IP de cada nodo, en cada nodo.\
Tambien deben tener acceso entre si, copiando la clave ssh de /root/.ssh/id\_rsa.pub de la maquina donde tenemos kubespray a /root/.ssh/authorized\_keysde cada nodo, asegúrate que todos los nodos tienen la misma hora, porque da problemas si alguno no esta en el mismo horario.\
\
Después de estos cambios, reiniciar los nodos normalmente.

```shell
sudo reboot
```

### &#x20;Despliegue del Cluster

Desde la maquina donde tenemos Kubespray, entramos en el directorio y ejecutamos

```shell
ansible-playbook -i inventory/NOMBRE_DE_TU_CLUSTER/hosts.yml --become --become-user=root cluster.yml -u root
```

Y empezara el despliegue, el tiempo aprox es de unos 30 minutos, a veces se puede extender en función de la versión del Kubernetes a desplegar, numero de nodos, ancho de banda, etc…

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Cuando termine, si todo ha salido correctamente, veremos una salida como esta, en este ejemplo, la duración ha sido de 25 minutos

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Si entramos en el nodo1 y ejecutamos el kubectl podemos ver que el cluster ya esta levantado

```shell
[rooto@nodo1 ~]# kubectl get nodes
NAME    STATUS   ROLES                  AGE   VERSION
node1   Ready    control-plane,master   18m   v1.23.12
node2   Ready    <none>                 16m   v1.23.12
node3   Ready    <none>                 16m   v1.23.12
```

### &#x20;Eliminación de un cluster existente

En este caso, debemos tener también los ficheros de configuración con las IPs de los nodos que componen el cluster, del mismo modo que lo hemos desplegado.\
\
El comando a usar para eliminar el cluster es

```shell
ansible-playbook -i inventory/NOMBRE_DE_TU_CLUSTER/hosts.yaml  --become --become-user=root reset.yml -u root
```

\
Y lo lanzamos, nada mas lanzarlo y hacer una comprobaciones, nos pedirá confirmación de si estamos seguro de que lo queremos eliminar.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Si le decimos que si “yes”, empezara la eliminación, el tiempo aprox es de unos 6 minutos, dependiendo de cuantas maquinas compongan el cluster, cuando termine debemos reiniciar manualmente cada nodo del cluster.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>
