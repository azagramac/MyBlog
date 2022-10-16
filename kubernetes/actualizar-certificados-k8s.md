# Actualizar certificados K8S



<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>



Lo primero verificamos cuando expiran los certificados actuales con el comando

```shell
kubeadm alpha certs check-expiration
```

\
Nos devolverá una salida como esta

```shell
# kubeadm alpha certs check-expiration
[check-expiration] Reading configuration from the cluster...
[check-expiration] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'

CERTIFICATE                EXPIRES                  RESIDUAL TIME   CERTIFICATE AUTHORITY   EXTERNALLY MANAGED
admin.conf                 Sep 30, 2022 12:30 UTC   7d                                    no      
apiserver                  Sep 30, 2022 12:30 UTC   7d            ca                      no      
apiserver-etcd-client      Sep 30, 2022 12:30 UTC   7d            etcd-ca                 no      
apiserver-kubelet-client   Sep 30, 2022 12:30 UTC   7d            ca                      no      
controller-manager.conf    Sep 30, 2022 12:30 UTC   7d                                    no      
etcd-healthcheck-client    Sep 30, 2022 12:30 UTC   7d            etcd-ca                 no      
etcd-peer                  Sep 30, 2022 12:30 UTC   7d            etcd-ca                 no      
etcd-server                Sep 30, 2022 12:30 UTC   7d            etcd-ca                 no      
front-proxy-client         Sep 30, 2022 12:30 UTC   7d            front-proxy-ca          no      
scheduler.conf             Sep 30, 2022 12:30 UTC   7d                                    no      

CERTIFICATE AUTHORITY   EXPIRES                  RESIDUAL TIME   EXTERNALLY MANAGED
ca                      Oct 01, 2028 10:25 UTC   8y              no      
etcd-ca                 Oct 01, 2028 10:25 UTC   8y              no      
front-proxy-ca          Oct 01, 2028 10:25 UTC   8y              no
```

Como vemos, tenemos los certificados a punto de caducar.\
\
Lo primero es revisar el estado del cluster con

```shell
kubectl get nodes
```

```shell
# kubectl get nodes
NAME    STATUS   ROLES     AGE    VERSION
nodo1   Ready    master    44d   v1.23.10
nodo2   Ready    worker1   44d   v1.23.10
nodo3   Ready    worker2   44d   v1.23.10
nodo4   Ready    worker3   44d   v1.23.10
```

\
Desde el nodo1 lanzamos

```shell
kubeadm alpha certs renew all
```

\
Reiniciamos el servicio kubelet

```shell
systemctl restart kubelet
```

y verificamos su estado, que no reporte errores.

```shell
# systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/usr/lib/systemd/system/kubelet.service; enabled; vendor preset: disabled)
  Drop-In: /usr/lib/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: active (running) since lun 2022-09-19 19:28:28 CEST; 1 weeks 0 days ago
     Docs: https://kubernetes.io/docs/
 Main PID: 18001 (kubelet)
    Tasks: 34
   Memory: 109.5M
   CGroup: /system.slice/kubelet.service
           ├─ 2973 /opt/cni/bin/calico
           └─18001 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kuberne...

sep 27 10:09:49 nodo3 kubelet[18001]: E0927 10:09:49.222698   18001 file.go:182] Not recursing into manifest path "/e...ackup"
sep 27 10:10:09 nodo3 kubelet[18001]: E0927 10:10:09.222717   18001 file.go:182] Not recursing into manifest path "/e...ackup"
sep 27 10:10:29 nodo3 kubelet[18001]: E0927 10:10:29.241301   18001 file.go:182] Not recursing into manifest path "/e...ackup"
```

\
Lanzamos el for, para parar los contenedores

```shell
for i in $(docker ps | egrep 'admin|controller|scheduler|api|etcd' | rev | awk '{print $1}' | rev);do docker stop $i; done
```

\
Hacemos un backup de la configuración del kubeconfig

```shell
mv $HOME/.kube/config $HOME/.kube/config.old
```

\
Copiamos el kubeconfig al home del usuario

```shell
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

\
Definimos el propietario del kubeconfig

```shell
chown $(id -u):$(id -g) $HOME/.kube/config
```

\
Y asignamos permisos al kubeconfig

```shell
chmod 600 $HOME/.kube/config
```

\
Sustituimos el nombre en el kubeconfig, si fuera necesario.

```shell
sed -i 's/master1/loadbalancer/g' $HOME/.kube/config
```

\
Verificamos el estado de los pods del cluster con

```shell
kubectl get pods -n kube-system
```

```shell
# kubectl get pods -n kube-system
NAME                              READY   STATUS    RESTARTS   AGE
coredns-78dc678595-25nhc          1/1     Running   1          7d16h
coredns-78dc678595-2fcwh          1/1     Running   0          17h
etcd-nodo1                        1/1     Running   995        7d17h
etcd-nodo2                        1/1     Running   91         7d17h                    1/1     Running   2          3d23h
kube-apiserver-nodo1              1/1     Running   4          6d15h
kube-apiserver-nodo2              1/1     Running   2          6d18h
kube-controller-manager-nodo1     1/1     Running   1443       6d23h
kube-controller-manager-nodo2     1/1     Running   2175       7d22h
kube-proxy-cg4lp                  1/1     Running   18         720d
kube-proxy-dnv65                  1/1     Running   23         720d
kube-scheduler-nodo1              1/1     Running   1461       6d23h
kube-scheduler-nodo2              1/1     Running   2051       7d17h
metrics-server-56c45cf9ff-tcrms   1/1     Running   15         7d15h
```

\
Y verificamos la fecha del nuevo certificado

```shell
kubeadm alpha certs check-expiration
```

```shell
# kubeadm alpha certs check-expiration
[check-expiration] Reading configuration from the cluster...
[check-expiration] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'

CERTIFICATE                EXPIRES                  RESIDUAL TIME   CERTIFICATE AUTHORITY   EXTERNALLY MANAGED
admin.conf                 Sep 27, 2023 10:36 UTC   365d                                    no      
apiserver                  Sep 27, 2023 10:36 UTC   365d            ca                      no      
apiserver-etcd-client      Sep 27, 2023 10:36 UTC   365d            etcd-ca                 no      
apiserver-kubelet-client   Sep 27, 2023 10:36 UTC   365d            ca                      no      
controller-manager.conf    Sep 27, 2023 10:36 UTC   365d                                    no      
etcd-healthcheck-client    Sep 27, 2023 10:36 UTC   365d            etcd-ca                 no      
etcd-peer                  Sep 27, 2023 10:36 UTC   365d            etcd-ca                 no      
etcd-server                Sep 27, 2023 10:36 UTC   365d            etcd-ca                 no      
front-proxy-client         Sep 27, 2023 10:36 UTC   365d            front-proxy-ca          no      
scheduler.conf             Sep 27, 2023 10:36 UTC   365d                                    no      

CERTIFICATE AUTHORITY   EXPIRES                  RESIDUAL TIME   EXTERNALLY MANAGED
ca                      Oct 04, 2030 15:28 UTC   8y              no      
etcd-ca                 Oct 04, 2030 15:28 UTC   8y              no      
front-proxy-ca          Oct 04, 2030 15:28 UTC   8y              no
```

\
Y con esto ya tendremos renovados los certificados del cluster. 😄
