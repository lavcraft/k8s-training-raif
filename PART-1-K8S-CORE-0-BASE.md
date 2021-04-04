Intro to Kubernetes
===================
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
---------------------------------------------------------------------

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
- Какие версии kubectl совместимы с какими версиями кластера?

K8S Authentication
------------------
- [ ] K8S _Реализации_ - PKS/TKGI/Cloud
- [ ] Как управлять кластерами? CLI or GUI ?
- [ ] kubectl CLI и много кластеров. Как понять где себя обслужить
- [ ] kubectl contexts
- [ ] tkgi cli

Hands-on practice quest #01: connect to existed cluster
-------------------------------------------------------
- [ ] Given пары участников
- [ ] When участники используют команды для извлечения информации из кластера

```shell
cat ~/.kube/config
```

```shell
kubectl config
```

```shell
kubectl config get-contexts
```

```shell
kubectl config use-contexts
```

```shell
tkgi login -a API -u USER -k
tkgi clusters
tkgi get-credentials CLUSTER_NAME
kubectl config get-contexts
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Что делать если команда не найдена?
- Как переключать кластеры без use-context?
- Как избежать ошибок выполнения команд не на "тех" кластерах?

K8S Concepts
------------
- [ ] Declarative vs Imperative
- [ ] Control loop (Observe, Orient, Decide, Act) Jonh Boyd
- [ ] Reconciliation
- Real control loop examples by kubectl - Desired state vs Current stats (Observe, diff ,act Control model)

Hands-on practice quest #02: I have an application, I wish deploy it to production!
-----------------------------------------------------------------------------------
- [ ] Given пары участников прошедших Docker тренинг
- [ ] When участники собирают приложение и деплоят в кластер
```shell
docker build
docker push
```

- [ ] Then участники делятся мыслями и соображениями
- куда нужно запушить образ чтобы kubernetes кластер его увидил?

K8S Concepts Network isolation
------------------------------
- [ ] Clusters and nodes
- [ ] Clusters network isolation
- [ ] Application in cluster resources
- [ ] Cluster network availability
- [ ] Docker run in Kubernetes and minimal configuration. Namespaces and pods fast introduction
- [ ] Демо kubectl apply/get/describe и output моды

Hands-on practice quest #03: Push and run forest
------------------------------------------------
- [ ] Given пары участников имеют собранные образы приложений
- [ ] When участники описывают поды с двумя сервисами и меняют состояние kubernetes
```shell
docker push <>
vi pod.yml
```

```shell
kubectl apply -f pod.yml
```

```shell
kubectl api-resources
```

- [ ] Then участники делятся результатами и соображениями
- Как узнать что там с нашим приложением и результатами выполнения команды?
- Если другой задеплоит под с таким же названием, он перезатрёт мой?
- А в какой namespace приложение задеплоилось?
- Как узнать какие проблемы у приложения при запуске?
- Что значат ограничения по CPU 200mi ? Это сколько?
- как понять какие ресурсы доступны для меня?
- как понять какие ресурсы работают в рамках неймспейса??
- как получить сырой вывод api?

K8S Application discovery
-------------------------
- [ ] Виды Service Discovery для приложений. Компромиссы каждого
- [ ] Роутинг трафика в кластер и в кластере. Взаимодействие внешнего балансировщика, NSX-T и K8S
- [ ] Доступность при деплое и политики обновления
- [ ] K8S Services and namespaces
- [ ] Демо kubectl exec curl с интерактивным watch для get pods/get svc -o wide

Hands-on practice quest #04: Redeploy application with services
---------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений
- [ ] When участники запускают команды и применяют новую настройки

Описываем сервисы
```shell
vi service.yml
# применяем их
kubectl apply -f service.yml
```

```shell
kubectl apply -f pod.yml
```

- [ ] Then участники делятся результатами и соображениями
- Как узнать что сервис настроен правильно и работает корректно?
- Рестартовало ли приложение после `kubectl apply -f pod.yml`?

K8S Internal and External access to containers
----------------------------------------------
- [ ] Containers Shell
- [ ] K8S Ingress и его описание
- [ ] kubectl exec
- [ ] Про то как собрать всё в кучку и альтернативные решения на примере kustomize и Helm.

Hands-on practice quest #05: Redeploy application with ingress
---------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов
- [ ] When участники запускают команды и применяют новую настройки

Описываем ingress
```shell
vi ingress.yml
# применяем их
kubectl apply -f ingress.yml
```

```shell
docker build <>
vi pod.yml
kubectl apply -f pod.yml
```

- [ ] Then участники делятся результатами и соображениями
- Как теперь достучаться до сервиса?
- Как попробовать посмотреть что внутри?
- Сколько инстансов каждого приложения?

K8S Namespace, Pods, Containers again and Scaling
-------------------------------------------------
- [ ] Containers Shell
- [ ] K8S Ingress и его описание
- [ ] ReplicaSet, Selector и TopologyKey
- [ ] kubectl exec
- [ ] Демо как собрать всё вместе

Hands-on practice quest #06: Redeploy application with replicas
---------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

```shell
vi pod.yml
kubectl apply -f pod.yml
curl <>
```

- [ ] Then участники делятся результатами и соображениями
- Как узнать в какое приложение попал запрос?
- Как посмотреть логи сразу со всех инстансов?
- Как распределены инстансы приложений?
- В какой момент приложение становится доступно для запроса curl?

K8S Application Probes and full deployments
-------------------------------------------
- [ ] liveness and rediness probess
- [ ] Application access model
- [ ] LB probes and application availability for user corner cases
- [ ] K8S Deploments

Hands-on practice quest #07: Redeploy application with probes
-------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Конфигурируем для одного приложение rediness/liveness для другого нет
```shell
tty0$ vi deployment.yml
tty1$ while true; do curl <app1>; sleep 1;done
tty2$ while true; do curl <app2>; sleep 1;done
tty0$ kubectl apply -f deployment.yml
```

- [ ] Then участники делятся результатами и соображениями
- Как узнать в какое приложение попал запрос?
- Как посмотреть логи сразу со всех инстансов?
- Как распределены инстансы приложений?
- В какой момент приложение становится доступно для запроса curl?
- Пробовали открыть UI?

K8S Multi path applications
---------------------------
- [ ] Basic Reverse proxy concepts
- [ ] Ingress rules - rewrite, split locations, certs

Hands-on practice quest #08: Redeploy application with custom location
----------------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Выносим приложения на разные пути одного домена
```shell
tty0$ vi ingress.yml
tty0$ kubectl apply -f ingress.yml
```

- [ ] Then участники делятся результатами и соображениями
- Пробовали открыть UI? Всё работает как нужно?
- Как можно исправить проблему с путями? (3 варианта)

K8S DNS mappings
----------------
- [ ] Подробно Forwarded RFC + расширения
- [ ] DNS TTL и как это влияет на maintenance приложений
- [ ] Локальность данных при использовании DNS Discovery
- [ ] Какой Discovery будет использовать наше приложение?
- [ ] K8S Config maps - Что это и где стоит использовать а где нет

Hands-on practice quest #09: Redeploy application with custom configuration
---------------------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Конфигурируем для одного приложение rediness/liveness для другого нет
```shell
vi configmap.yml
vi deployment.yml
kubectl apply -f configmap.yml
kubectl apply -f deployment.yml
```

- [ ] Then участники делятся результатами и соображениями
- нужен ли рестарт приложения после применения configmap? Обоснуйте так лучше?
- какие проблемы могут быть с обращением через DNS имя?
- есть ли разница DNS имя сервиса?
- что насчёт балансировки TCP/UDP?
- как посмотреть какие сейчас есть балансировщики?

K8S Test Distributed Application and pods states
------------------------------------------------
- [ ] Типичные трудности в распределённых приложениях при масштабировании
- [ ] OpenTelemetry и трейсинг

Hands-on practice quest #10: Redeploy application and start stress testing
--------------------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Конфигурируем для одного приложение rediness/liveness для другого нет
```shell
kubectl apply -f final_deployment.yml
./start_stress.sh
```

- [ ] Then участники делятся результатами и соображениями
- Всё ли хорошо с приложениями?
- Как посмотреть последине свалившиеся приложения?

Hands-on practice quest #11: Redeploy application and start stress testing
--------------------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress и завершили задания
- [ ] When участники запускают команды и применяют новую настройки


- [ ] Then участники делятся результатами и соображениями
- Почему под не стартует? как узнать?
- Почему сервис может быть не доступен?

K8S Jobs Management and workload distribution
------------------------------------------------
- [ ] Jobs Scheduling и специфичные виды запуска
- [ ] DaemonSets, CronJobs
- [ ] High Level альтернативы. Argo workflow etc.
- [ ] Демо запуск нагрузки на наше приложение через jobs

Hands-on practice quest #12: Run dependant jobs
--------------------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress и завершили задания
- [ ] When участники запускают команды и применяют новую настройки

```shell
vi jobs-test.yaml
kubectl appy -f jobs-test.yaml
kubectl describe <>
```

- [ ] Then участники делятся результатами и соображениями
- [ ] можно ли запускать job вместе с replica controller?
- [ ] как что-то сделать при завершение Job ? 
- [ ] а если что-то пошло не так?

K8S Helm Charts
---------------
- [ ] Предпосылки к появлению
- [ ] Что было "ДО"
- [ ] Версии Helm Charts
- [ ] На что обращать внимание при использовании

K8S Helm Charts
---------------
- [ ] Предпосылки к появлению. Отсылки к kustomize.io.
- [ ] Что было "ДО"
- [ ] Версии Helm Charts
- [ ] На что обращать внимание при использовании

