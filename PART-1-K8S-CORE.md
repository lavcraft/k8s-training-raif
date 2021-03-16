Intro to Kubernetes
===============
8 часов, 2 ак. дня.

Prerequisites
=============

- [ ] RAM ≥ 8Gb
- [ ] [Пройденыне задание с тренига по Docker](#link-to-docker-part)
- [ ] [Развёрнутый кластер k8s на пару](#link-to-confluence)
- [ ] terminal
- [ ] kubectl cli

Agenda
======
Введение в K8S
-----------------

- [ ] Зачем нужен K8S? Функции и зачем нам оркестрация.
- [ ] Процесс ОС "на стероидах": в чём отличие от локального запуска, что K8S делает за меня?
- [ ] Метафора морского порта
- [ ] Текущее состояние дел K8S и направление развития
- [ ] Парадигма Cloud Native (DevOps+CI/CD+Microservices+Containers) приложений (отсылки к тренингу по Docker и 12
  factor apps)
- [ ] K8S и приложения - Capabilities (Self-Service, API-Driven, Elastic)
- [ ] Основные компоненты и с чем мы будем работать на этом тренинге
- kubectl

Hands-on practice quest #00: requisites check and compatibility check
---------------------------

- [ ] Given сформированы пары участников
- [ ] When участники используют команды и проанализируют их вывод и поведение

```shell
kubectl
```

```shell
docker version
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Какая версия kubectl нужна для установленного кластера?
- Какие версиии kubectl совместимы с какими версиями кластера?

K8S Authentication
------------------
- [ ] Как управлять кластерами? CLI or GUI ?
- [ ] kubectl CLI и много кластеров. Как понять где себя обслужить
- [ ] kubectl contexts

Hands-on practice quest #01: connect to existed cluster
-------------------------------------------------------
- [ ] Given пары участников
- [ ] When участники используют команды для извлечения информации из кластера

```shell
kubectl config
```

```shell
docker config get-contexts
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Что делать если команда не найдена?
- Как переключать кластеры без use-context?
- Как избежать ошибок выполнения команд не на "тех" кластерах?

K8S Concepts
------------
- [ ] Declarative vs Imperative
- [ ] Control loop (Observe, Orient, Decied, Act) Jonh Boyd
- [ ] Reconciliation
- Real control loop examples by kubectl - Desired state vs Current stats (Observe, diff ,act Control model)

Hands-on practice quest #02: I have an application, I wish deploy it to production!
-------------------------------------------------------
- [ ] Given пары участников прошедших Docker тренинг
- [ ] When участники собирают приложение и деплоят в кластер
```shell
docker build
docker push
```

- [ ] Then участники делятся мыслями и соображениями
- куда нужно запушить образ чтобы kubernetes кластер его увидил?

K8S Concepts Network isolation
------------
- [ ] Clusters and nodes
- [ ] Clusters network isolation
- [ ] Application in cluster resources
- [ ] Cluster network availability
- [ ] Docker run in Kubernetes and minimal configuration. Namespaces and pods fast introduction

Hands-on practice quest #03: Push and run forest
-------------------------------------------------------
- [ ] Given пары участников имеют собранные образы приложений
- [ ] When участники описывают поды и деплоят в kubernetes
```shell
vi pod.yml
```

```shell
kubectl apply -f pod.yml
```

- [ ] Then участники делятся результатами и соображениями
- Как узнать что там с нашим приложением и результатами выполнения команды?
- Если другой задеплоит под с таким же названием, он перезатрёт мой?
- А в какой namespace приложение задеплоилось?
