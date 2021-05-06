Kubernetes Network Base
=======================
4 часов, 1 ак. дня.

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
Kubernetes Network Base
-----------------------

- [ ] работа с удалёнными приложениями - port forwarding
- [ ] доступы между приложениями
- [ ] kube-proxy и iptables/conntrack механизмы
- [ ] CNI Networks. Что это и причём тут Linux? Какие есть точки расширения?
- [ ] CNI реализации. NSX-T, calicao, flannel, etc и зачем так много?
- [ ] Как NSX-T + NCP. OSI и разные уровни маршрутизации на основе Ingress Resource + Ingress Controller
- [ ] Виртуальные устройства Linux veth* и бриджы cbr* и где же всё таки наш CNI?

Hands-on practice quest #00: работа с портами приложений - containerPort/targetPort/nodePort
--------------------------------------------------------

- [ ] Given сформированы пары участников
- [ ] When участники используют команды и проанализируют их вывод и поведение

```shell
cat deployment.yaml
```

```shell

```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы

- как связаны targetPort в сервисе и containerPort?
- Какие версии kubectl совместимы с какими версиями кластера?

Виды взаимодействия через сеть
------------------------------

- [ ] Cross node pod to pod
- [ ] Services Discovery
- [ ] Services exposure for external access
- [ ] Network restrictions and security
- [ ] High Availability

Hands-on practice quest #00: работа с портами приложений - containerPort/targetPort/nodePort
--------------------------------------------------------------------------------------------

- [ ] Given сформированы пары участников
- [ ] When участники используют команды и проанализируют их вывод и поведение

```shell
cat deployment.yaml
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы

- как связаны targetPort в сервисе и containerPort?
- Какие версии kubectl совместимы с какими версиями кластера?
- по какому порту можно достучаться до приложения?

Доступ на удалённые API
-----------------------

- [ ] Как работае Port Forwarding
- [ ] Когда это может понадобиться?

Hands-on practice quest #01: доступ к внутренним API
----------------------------------------------------

```shell
kubectl get all
```

```shell
kubectl port-forward deployments/<> 8080:8000
```


High Availability и безопасный деплой
-------------------------------------

- [ ] Про Blue/Green деплоймент и canary releases
- [ ] Что для этого есть в containers spec? Update strategy rollingUpdate
- [ ] А какой используется у нас?
- [ ] Как поменять один

Hands-on practice quest #02: доступ к внутренним API
----------------------------------------------------

```shell
tty0$ kubectl get all
tty0$ watch -n0.1 kubectl get rs
```

```shell

tty1$ kubectl edit deployment/<>
tty1$ kubectl port-forward deployment/<>
tty1$ curl <>
tty1$ kubectl rolling undo
tty1$ curl <>
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы

- Было ли доступно приложение при деплое?
- Кто управляет распределением запросов между контейнерами?
- Гарантированно ли то что запрос не попадёт на не рабочий контейнер?


Ограничение сетевого доступа NetworkPolicy
------------------------------------------

- [ ] Где разграничиваем доступ - egress/ingress
- [ ] как выбираем на кого применяем policy

Hands-on practice quest #01: Настройка NetworkPolicy
----------------------------------------------------

```shell
kubectl get NetworkPolicies
```

```shell
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name:
  namespace:
spec:
  ingress:
    from:
  egress:
    to: 
  podSelector:
  policyTypes:
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы

- Как применять дефолтные NetworkPolicy для приложений?
- Как сделать односторонний доступ из приложения?