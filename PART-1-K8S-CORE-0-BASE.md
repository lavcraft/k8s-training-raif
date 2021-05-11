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
- [] **Демо**. CLI и настройки доступа к кластеру

Hands-on practice quest #00: requisites check and compatibility check
---------------------------------------------------------------------

- [ ] Given сформированы пары участников
- [ ] When участники используют команды и проанализируют их вывод и поведение

```shell script
kubectl
docker version
docker login artifactory.raiffeisen.ru
```

```shell script
ls -la ~/.kube
kubectl cluster-info
```

```shell script
kubectl completion bash
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Какая версия kubernetes кластера у вас?
- Какая версия kubectl нужна для установленного кластера?
- Какие версии kubectl совместимы с какими версиями кластера?
- [Enable kubectl completion howto](https://kubernetes.io/docs/tasks/tools/included/optional-kubectl-configs-bash-linux/#enable-kubectl-autocompletion)

K8S Authentication
------------------
- [ ] K8S _Реализации_ - PKS/TKGI/Cloud
- [ ] Как управлять кластерами? CLI or GUI ?
- [ ] kubectl CLI и много кластеров. Как понять где себя обслужить
- [ ] kubectl config get-contexts
- [ ] tkgi cli

Hands-on practice quest #01: connect to existed cluster
-------------------------------------------------------
- [ ] Given пары участников
- [ ] When участники используют команды для извлечения информации из кластера

```shell script
cat ~/.kube/config
```

```shell script
kubectl config view
```

```shell script
kubectl config get-contexts
```

```shell script
kubectl config use-contexts
kubectl auth can-i --list
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Что делать если команда не найдена?
- Как переключать кластеры без use-context?
- Как избежать ошибок выполнения команд не на "тех" кластерах?
- Как разделены ресурсы других участников во дном кластере?
- \* попробуйте использовать несколько конфигураций с помощью переменной окружения `KUBECONFIG`. Что будет с разными кластерами при выводе `kubectl config view`?

## K8S Concepts

- [ ] Declarative vs Imperative
- [ ] Control loop (Observe, Orient, Decide, Act) Jonh Boyd
- [ ] Reconciliation
- Real control loop examples by kubectl - Desired state vs Current stats (Observe, diff ,act Control model)
- Материалы
  - [Настрокйка реквизитов docker-registry k8s](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)


### Hands-on practice quest #02: I have an application, I wish deploy it to production!

- [ ] Given пары участников прошедших Docker тренинг
- [ ] When участники собирают приложение и деплоят в кластер
```shell script
kubectl create job test --image=busybox -- echo "Hello World"
```

```shell script
kubectl create job test --image=artifactory.raiffeisen.ru/ext-techimage-docker/busybox -- echo "Hello World" 

kubectl get pods
kubectl get jobs

kubectl get logs jobs/test
```

```shell script
# Попробуем запустить образ из репозиторя, требущего авторизацию
kubectl run -it debug --image=artifactory.raiffeisen.ru/ext-rbru-container-community-docker/cli-tools -- /bin/sh

cat ~/.docker/config.json

kubectl get events
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=$USER/.docker/config.json> \
    --type=kubernetes.io/dockerconfigjson
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "regcred"}]}'

kubectl run -it debug --image=artifactory.raiffeisen.ru/ext-rbru-container-community-docker/cli-tools -- /bin/sh

kubectl get pods
```

- [ ] Then участники делятся мыслями и соображениями
- куда нужно запушить образ чтобы kubernetes кластер его успешно спулил?
- какой правильный процесс деплоя образов?
- чем отличается Job от Pods?
- когда удаляется Pod созданный при создании Job?
- кто ответственнен за это удаление?
- \* запустить под с лимитами по памяти в 16mb

## K8S Concepts Network isolation

- [ ] Clusters and nodes
- [ ] Clusters network isolation
- [ ] Application in cluster resources
- [ ] Cluster network availability
- [ ] Docker run in Kubernetes and minimal configuration. Namespaces and pods fast introduction
- [ ] Демо kubectl apply/get/describe и output моды
- [Подробно о ресурсах и CGroups](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html-single/resource_management_guide/index)

### Hands-on practice quest #03: Run apps in cluster
- [ ] Given пары участников имеют собранные образы приложений
- [ ] When участники описывают поды с двумя сервисами и меняют состояние kubernetes


```shell script
cat handson/handson-03/apps-01.yml
kubectl apply -f handson/handson-03/apps-01.yml
```

- [ ] Then участники делятся результатами и соображениями
- [ ] Какой статус пода?
- [ ] Найдите ошибку
- Как исправить?

```shell script
 kubectl
```

```shell script
# INFO: Не забываем что можно указать дефолтный контекст
kubectl config set-context --current --namespace=<>
```

```shell script
kubectl edit app-butter
# add limits 4mb
vi handson/handson-03/apps-01.yml
kubectl apply -f handson/handson-03/apps-01.yml
kubectl get pods
```

- [ ] Then приложение не запуcтилось
- [ ] Найти информацию для извлечения признака проблемы
- [ ] Найти оптимальное значение лимитов и исправить
- [ ] * Вывести статусы всех ошибочных статусов приложений в форме `image - status`. Смотри документацию jsonpath/go-template [kubectl jsonpath doc](https://kubernetes.io/docs/reference/kubectl/jsonpath/)
- [ ] * Задайте дефолтные квоты для приложений в вашем namespace


- [ ] Then участники делятся результатами и соображениями
- Как узнать что там с нашим приложением и результатами выполнения команды?
- Если другой задеплоит под с таким же названием, он перезатрёт мой?
- А в какой namespace приложение задеплоилось?
- А на каком узле запустилось?
- Как узнать какие проблемы у приложения при запуске?
- Что значат ограничения по CPU 200mi ? Это сколько?
- как понять какие ресурсы доступны для меня?
- как понять какие ресурсы работают в рамках неймспейса??
- как получить сырой вывод api?
- получилось ли у вас отредактировать лимиты приложения? Какие были трудности?

K8S Application discovery
-------------------------
- [ ] Виды Service Discovery для приложений. Компромиссы каждого
- [ ] Роутинг трафика в кластер и в кластере. Взаимодействие внешнего балансировщика, NSX-T и K8S
- [ ] Доступность при деплое и политики обновления
- [ ] K8S Services and namespaces
- [ ] Демо kubectl exec curl с интерактивным watch для get pods/get svc -o wide
- [kubectl port-forward docs](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)

Hands-on practice quest #04: Redeploy application with services
---------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений
- [ ] When участники запускают команды и применяют новую настройки

```shell
cat  handson/handson-04/apps.yml
kubectl apply -f  handson/handson-04/apps.yml
kubectl get pod debug
```

```shell
cat handson/handson-04/service.yml
# применяем их
kubectl apply -f  handson/handson-04/service.yml
```

- [ ] Допишете сервис для недостающего приложения
- [ ] Запустить cli-tools если под не запущен
- [ ] Как проверить их работособность?
- [ ] Задание: Запустить cli-tools если под не запущен
- [ ] Задание: Вычислите IP сервиса

```shell
kubectl get pods
[debug] $ nslookup <pod-ip>
[debug] $ nslookup <service-ip>
```

- [ ] Then nslookup выдал обратные зоны по запрашиваемым IP
- [ ] Кто ответственнен за это преобразование?
- [ ] Как найти кто ответственен?
- [ ] Можно ли его переконфигурировать?
- [ ] Demo 
- [ ] Задание: Найти кто влияет на название DNS имени

```shell script
[debug] $ cat /etc/resolv.conf
kubectl get services --all-namespaces | grep <nameserver-ip>
kubectl get pods --namespace=kube-system -l k8s-app=kube-dns
```

  
- [ ] Then участники делятся результатами и соображениями
- Как узнать что сервис настроен правильно и работает корректно?
- Рестартовало ли приложение после `kubectl apply -f pod.yml`?
- Что будет если изменить metadata.labels.app ?
- Что если остановить приложение и обратиться к сервису?
- Кто настраивает kube-dns ?
- Как диагнастировать kube-dns ?

K8S Internal and External access to containers
----------------------------------------------
- [ ] Containers Shell
- [ ] K8S Ingress и его описание
- [ ] kubectl exec
- [ ] [Подробнее об ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

Hands-on practice quest #05: Redeploy application with ingress
---------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов
- [ ] When участники запускают команды и применяют новую настройки

- [ ] Задание: Настроить доступ из сети банка к приложению

```shell script
# тест должен заработать
watch -n0.1 http://app-knife-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
# Завершите ingress конфигурацию
vi handson/handson-05/ingress.yml
kubectl apply -f handson/handson-05/ingress.yml
```

- [ ] Then участники делятся результатами и соображениями
- Как теперь достучаться до сервиса?
- Что будет если остановить сервис?
- Как попробовать посмотреть что внутри?
- Сколько инстансов каждого приложения?
- Как ingress связан с ingress-controller?
- Где находится ingress-controller?
- Кто отвечает за "анонсирование" внутренних имён сервисов и имён для ingress?

K8S Namespace, Pods, Containers again and Scaling
-------------------------------------------------
- [ ] ReplicaSet, Deployment и TopologyKey
- [ ] Как собрать всё вместе

Hands-on practice quest #06: Redeploy application with replicas
---------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

```shell
# запускаем тестовую команду. Видим что падает
[tty0] $ watch -n0.1 app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
# применяем деплоймент
[tty1] $ kubectl apply -f handson/handson-06/deployment.yml
# после запуска снова запускаем
[tty0] $ watch -n0.1 app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
```

- [ ] Задание: Изменить количество реплик для деплоймента app-butter-deployment на 2 с помощью команды `kubectl scale`

- [ ] Then тест упал при увеличении количества реплик когда сервис вернул HTTP 500
- [ ] Почему упал тест? Разберитесь в источнике проблемы
- [ ] Участники делятся результатами и соображениями
- Как узнать в какое приложение попал запрос?
- Как посмотреть логи сразу со всех инстансов?
- Как распределены инстансы приложений?
- В какой момент приложение становится доступно для запроса curl?
- Как вы думаете, какой вид балансировки используется при вызове app-butter из app-knife?

K8S Application Probes and full deployments
-------------------------------------------
- [ ] liveness and readiness probess
- [ ] Application access model
- [ ] LB probes and application availability for user corner cases
- [ ] K8S Deploments

Hands-on practice quest #07: Redeploy application with probes
-------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Конфигурируем для одного приложение rediness/liveness для другого нет
```shell
[tty0] $ watch -n0.1 app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru

[tty1] $ kubectl edit deployment.apps app-butter-deployment
[tty1] $ kubectl apply -f deployment.yml
```

- [ ] Then участники делятся результатами и соображениями
- Как узнать в какое приложение попал запрос?
- Как посмотреть логи сразу со всех инстансов?
- Как распределены инстансы приложений?
- В какой момент приложение становится доступно для запроса curl?
- Будут ли доходить запросы до приложения если livenessProbe отрицательна а readinessProbe положительна?
- * HAL API: как быть с обратными ссылками? Как узнать с каого адреса пришёл запрос?

Hands-on practice quest #08: Edit deployment
-------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Изменим metadata.labels.app
```shell
[tty0] $ kubectl get pods -w
[tty1] $ kubectl edit pod app-butter-<>
[tty1] $ kubectl get pods
```

Вернём обратно metadata.labels.app
```shell
kubectl edit pod app-butter-<>
kubectl get pods
```

- [ ] Then участники делятся результатами и соображениями
- Что случилось со старым приложением при изменении labels?
- Объясните поведение
- Будет ли работать replica set?

K8S Deployment rollout
-------------------------------------------
- [ ] kubectl rollout undo demo

Hands-on practice quest #07.2: deployment rollout
-------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Изменим metadata.labels.app
```shell
[tty0] $ kubectl get rs -w
[tty1] $ kubectl rollout history deployment app-butter-deployment
[tty1] $ kubectl rollout undo deployment app-butter-deployment --to-revision=<revision-number>
[tty1] $ kubectl get deployment app-butter-deployment -o yaml
```

- [ ] Then участники делятся результатами и соображениями
- Можно ли откатиться на уже откаченную версию (последнюю)?
- Как посмотреть все deployments на кластере?

K8S Multi path applications
---------------------------
- [ ] Basic Reverse proxy concepts
- [ ] Ingress rules - rewrite, split locations, certs

Hands-on practice quest #08: Redeploy application with custom location
----------------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки
- [ ] Задание: сделать новый ingress, чтобы приложения работали на одном хосте

INFO: [Nginx ingress docs](https://kubernetes.io/docs/concepts/services-networking/ingress/)
INFO: [Nginx ingress path mapping](https://kubernetes.github.io/ingress-nginx/user-guide/ingress-path-matching/)


```shell
# Тест должен заработать
[tty0] $ watch -n0.5 curl training-app.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru

# Выносим приложения на разные пути одного домена
tty1$ vi ingress.yml
tty1$ kubectl apply -f ingress.yml
```

- [ ] Задание: просмотрите логи всех приложений app-butter. Какие заголовки приходят в приложение?

- [ ] Then участники делятся результатами и соображениями
- Пробовали открыть UI? Всё работает как нужно?
- Как можно исправить проблему с путями? (3 варианта)
- Достаточно ли заголовков которые приходят для восстановления контекста запроса?

K8S ConfigMaps
----------------
- [ ] K8S ConfigMaps
- [ ] Подробно Forwarded RFC + расширения
- [ ] DNS TTL и как это влияет на maintenance приложений
- [ ] Локальность данных при использовании DNS Discovery
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

