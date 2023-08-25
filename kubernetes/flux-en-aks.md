# Flux en AKS



```sh
az k8s-extension create -g NOMBRE_RESOURCE_GROUP -c Signature -t ManagedClusters --name NOMBRE_FLUXel --extension-type microsoft.flux --config kustomize-controller.enabled=true
```

<figure><img src="../.gitbook/assets/image (2) (3).png" alt=""><figcaption></figcaption></figure>
