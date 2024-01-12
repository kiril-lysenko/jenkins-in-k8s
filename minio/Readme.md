# Minio

_Основано на [Bitnami Object Storage Helm Chart based on MinIO](https://github.com/bitnami/charts/tree/master/bitnami/minio/#installing-the-chart)_

![](images/sh1.jpg)

В качестве хранилища будет использоваться диски сервера. Поэтому необходимо жестко привязать
поды minio к соответствующим нодам кластера. Пометим три node при помощи label.

```shell
kubectl label nodes node2 minio=yes
kubectl label nodes node3 minio=yes
kubectl label nodes node4 minio=yes
```

Учитываем, что для нормальной работы minio должны быть 9 физических хранилища. Т.е. в 
нашем случае, на каждой ноде будет по два диска для minio.

## Установка
### kubectl

```shell
kubectl create ns minio
kubectl -n minio apply -f manifests/
```


## WEB interface

Поскольку был установлен ingress, доступ к WEB интерфейсу:

http://minio.cluster.local
