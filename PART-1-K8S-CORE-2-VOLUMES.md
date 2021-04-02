Kubernetes Volumes
==================
2 часов, 1 ак. дня.

Prerequisites
=============

- [ ] RAM ≥ 8Gb
- [ ] [Пройденыне задание с тренига по Docker](#link-to-docker-part)
- [ ] [Пройденыне задание с тренига по K8S-CORE](#link-to-docker-part)
- [ ] [Развёрнутый кластер k8s на пару](#link-to-confluence)
- [ ] terminal
- [ ] kubectl cli


Agenda
======
Kubernetes Stateful Apps
------------------------

- [ ] PO/PV/PVC связь
- [ ] концепция POD и Volumes.
- [ ] пример модели producer/consumer Pod

Hands-on practice quest #00: поиск pv/pvc/po
--------------------------------------------

```shell
kubectl api-resources
```

```shell
vi deployment.yml 
```

```shell
kubectl apply -f deployment.yml 
```


- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы

- как вы думаете, почему PV не namespaced?
- можем ли запрашивать ресурсы из отличных от текущего namespace находясь в контейнере?

Kubernetes Stateful Apps
------------------------

- [ ] K8S StorageClass и какие доступны нам
- [ ] Static vs Dynamic storage provisioning
- [ ] пример модели producer/consumer Pod

Hands-on practice quest #00: поиск pv/pvc/po
--------------------------------------------

```shell
kubectl api-resources
```

```shell
vi deployment.yml 
```

```shell
kubectl apply -f deployment.yml 
```


- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы

- как вы думаете, почему PV не namespaced?
- можем ли запрашивать ресурсы из отличных от текущего namespace находясь в контейнере?

