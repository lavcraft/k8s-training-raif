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
# выведем "спецификацию" для авто комплита
kubectl completion bash
# применим её
source <(kubectl completion bash)
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Какая версия kubernetes кластера у вас?
- Какая версия kubectl нужна для установленного кластера?
- Какие версии kubectl совместимы с какими версиями кластера?
- Откуда берётся папка `~/.kube` ?
- Есть ли разница в какой оболочке запускать команды? BASH/ZSH/FISH/ETC
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
- [ ] Задача: узнать список ваших привилегий в кластере

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
[Подробнее про права и RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#role-binding-examples)

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Что делать если команда не найдена?
- Как я авторизовываюсь в кластере?
- Кто может выдавать права доступа к кластера и в чём заключается "передача"?
- Можно ли авторизоваться в kubernetes с помощью Ouath 2.0/OIDC т.е через "Соцсеточки"?
- Как переключать кластеры без use-context?
- Как избежать ошибок выполнения команд не на "тех" кластерах?
- Как разделены ресурсы других участников во дном кластере?
- Как выбрать свой namespace пол умолчанию?
- \* попробуйте использовать несколько конфигураций с помощью переменной окружения `KUBECONFIG`. Что будет с разными кластерами при выводе `kubectl config view`? [О KUBECONFIG](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

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

- [ ] Задание: Понять сработала ли наша задача
- [ ] Почему не вывелись логи?
- [ ] Почему у пода такое странное имя?

```shell script
kubectl describe pod test-<id>

kubectl create job test --image=artifactory.raiffeisen.ru/ext-techimage-docker/busybox -- echo "Hello World" 

kubectl get pods
kubectl get jobs

kubectl logs jobs/test
kubectl delete job test
kubectl get pods
```

- [ ] в чём различие в семантике написания команд - `kubectl get job test` и `kubectl get jobs/test`? Попробуйте объяснить
- [ ] что будет если команду вывода логов разными способами?
- [ ] удаляется ли созданный pod после удаления job test? 
- [ ] Задание: запустить образ с curl, dig прочими инструментами для дебага

```shell script
# Попробуем запустить образ из репозиторя, требущего авторизацию
# INFO: Пригодится нам в будущем для исследовательских целей
kubectl run -it debug --image=artifactory.raiffeisen.ru/ext-rbru-container-community-docker/cli-tools -- /bin/sh

cat ~/.docker/config.json

kubectl get events
# Если у вас был docker login
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=/home/$USER/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson
# если не было - ввести логин пароль вручную https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line
kubectl create secret docker-registry regcred --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>
# <your-registry-server> - например https://artifactory.raiffeisen.ru
kubectl explain serviceaccount.imagePullSecrets
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
- \* запустить под с лимитами по памяти в 16mb. [About K8S Limits](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)

## K8S Container isolation

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
- [ ] Задание: Исправьте ошибку пользуясь информацией `kubectl explain`

```shell script
# INFO: Не забываем что можно указать дефолтный контекст
kubectl config set-context --current --namespace=<>
kubectl explain pod.spec
kubectl edit butter-fabric
```

- [ ] Какие возникли трудности при редактировании? Объясните почему так?
- [ ] Задание: передеплоить приложения отдельно

```shell script
kubectl delete -f handson/handson-03/apps-01.yml
kubectl apply -f handson/handson-03/apps-02.yml
kubectl explain pod.spec.containers.resources
# add limits 4mb и cpu 200mi
kubectl edit pod app-butter
vi handson/handson-03/apps-02.yml
kubectl apply -f handson/handson-03/apps-02.yml
kubectl get pods
```
[Про установки лимитов](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)

- [ ] Then приложение не запуcтилось
- [ ] Найти информацию для извлечения признака проблемы
- [ ] Найти оптимальное значение лимитов и исправить
- [ ] \* Вывести все поды с ошибочным статусом в формате `name/image - status`. Смотри документацию jsonpath/go-template [kubectl jsonpath doc](https://kubernetes.io/docs/reference/kubectl/jsonpath/)
- [ ] \* Задайте дефолтные квоты для приложений в вашем namespace


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

K8S Application networking
-------------------------------
- [ ] Виды Service Discovery для приложений. Компромиссы каждого
- [ ] Роутинг трафика в кластер и в кластере. Взаимодействие внешнего балансировщика, NSX-T и K8S
- [ ] Доступность при деплое и политики обновления
- [ ] K8S Services and namespaces
- [ ] Демо kubectl exec curl с интерактивным watch для get pods/get svc -o wide
- [kubectl port-forward docs](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)

Hands-on practice quest #04: Access to application via services
---------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений
- [ ] When участники запускают команды и применяют новую настройки
- [ ] Задание: Запустить cli-tools если отсутствует

> Для выполнения задания требуются приложения из [handson #03](#hands-on-practice-quest-03-run-apps-in-cluster)

```shell
# проверим что есть под debug, иначе запустим (см handson #02)
kubectl get pod debug 
# Откроем новую консоль
# Зайдём в контейнер и будем него будем производить дебаг наших приложений
kubect exec -it debug -- /bin/bash
...
```

1. Задание: С помощью материалов ниже вычислите IP адресс и имя запущенного Pod и сделать запрос к сервису app-knife
1. [Debugging DNS Resolution](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
1. [DNS Pod Service](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pods)
1. Задание: Допишете сервис для недостающего приложения и повторите запрос к app-knife

```shell
cat handson/handson-04/services.yml
kubectl explain service.spec
kubectl expose --help
# применяем их
kubectl apply -f  handson/handson-04/services.yml
kubectl get services
kubectl get endpoints
```

- [ ] Задание: Проверить работоспособность сервиса? ([О метаданных и labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#motivation))
- [ ] Работает ли `kubectl port-forward pod/app-knife 8080` если не задан `containerPort`?
- [ ] На что влияет containerPort в pod.spec.containers.ports.containerPort?
- [ ] Что будет если при работающей команде `kubectl port-forward pod/app-knife 8080` перезапустить pod app-knife?
- [ ] Задание: Заставить сервис `app-butter-service` возвращать json ответ. (Совет: попробуйте лучше понять, отвечая на ответы дальше по списку)
- [ ] Объясните ответы от сервиса app-knife в случаях: 
  1. не сервиса app-butter-service.
  2. есть сервис app-butter-service но нет labels у app-butter. (Воспользуйтесб debug подом и посмотрите что возвращает app-butter-service)
  3. есть и сервис и соответсвующий ему labels.app? (Попробуйте воспользоваться командой `kubectl edit pod app-butter` и добавить `metadata.labels.app=app-butter`
- [ ] Можно ли включить интерактивный шел в запущенном контейнере?
- [ ] Как выполнить команду в запущенном контейнере не заходя в него?

```shell
kubectl get pods
[debug] $ nslookup <pod-ip>
[debug] $ nslookup <pod-name>
[debug] $ nslookup <service-ip>
[debug] $ nslookup <service-name>
```

- [ ] Then nslookup выдал обратные зоны по запрашиваемым IP
- [ ] Кто ответственнен за это преобразование?
- [ ] Как найти кто ответственен?
- [ ] Можно ли его переконфигурировать?
- [ ] Demo 
- [ ] Задание: Найти кто влияет на название DNS имени

[man resolv.conf](https://man7.org/linux/man-pages/man5/resolv.conf.5.html)

```shell script
[debug] $ cat /etc/resolv.conf
kubectl get services --all-namespaces | grep <nameserver-ip>
kubectl get pods --namespace=kube-system -l k8s-app=kube-dns
```

  
- [ ] Then участники делятся результатами и соображениями. Контрольные вопросы блока:
- Как узнать что сервис настроен правильно и работает корректно?
- Рестартовало ли приложение после повторного `kubectl apply -f pod.yml` и почему?
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
watch -n0.1 curl http://app-knife-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
kubect explain ingress.spec
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
- [ ] ReplicaSet
- [ ] Deployment
- [ ] TopologyKey

Hands-on practice quest #06: Redeploy application with replicas
---------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки
- [ ] Задание: изменить запуск приложений в поде на запуск c помощью deployment

```shell
# запускаем тестовую команду. Видим что падает
[tty0] $ watch -e -n0.1 curl --fail app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
[tty1] kubectl explain deployment
# применяем деплоймент
[tty1] $ cat handson/handson-06/deployment.yml
[tty1] $ vi handson/handson-06/deployment.yml
[tty1] $ kubectl apply -f handson/handson-06/deployment.yml
# после запуска снова запускаем команду для проверки
[tty0] $ watch -e -n0.1 curl --fail app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
```

- [ ] Исследуем надёжность нашего деплоймента

- [ ] Задание: Изменить количество реплик для deployment app-butter-deployment на 3 с помощью команды `kubectl scale`

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
[tty0] $ watch -e -n0.1 curl --fail app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru

[tty1] $ kubectl edit deployment app-butter-deployment
[tty1] $ kubectl apply -f deployment.yml
```

- [ ] Then участники делятся результатами и соображениями
- Как узнать в какое приложение попал запрос?
- Как посмотреть логи сразу со всех инстансов?
- Как распределены инстансы приложений?
- В какой момент приложение становится доступно для запроса curl?
- Будут ли доходить запросы до приложения если livenessProbe отрицательна а readinessProbe положительна?
- Достаточно ли только readiness для надёжной работы app-butter?
- * HAL API: как быть с обратными ссылками? Как узнать с какого адреса пришёл запрос?

Hands-on practice quest #7.1: Edit deployment
-------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Изменим metadata.labels.app на app-knife-edited
```shell
[tty0] $ kubectl get pods -w
[tty1] $ kubectl edit pod app-butter-<custom-id-autocomplete-it>
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
- Как удалить все изменённые поды?

K8S Deployment rollout
-------------------------------------------
- [ ] Отличия Deployment от ReplicaSet и рекомендации
- [ ] kubectl rollout undo demo

Hands-on practice quest #07.2: deployment rollout
-------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Изменим metadata.labels.app
```shell
[tty0] $ kubectl get rs -w # rs = replicaset
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

Hands-on practice quest #08: Two apps in one domain
----------------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки
- [ ] Задание: сделать новый ingress, чтобы приложения работали на одном хосте

INFO: [Nginx ingress docs](https://kubernetes.io/docs/concepts/services-networking/ingress/)
INFO: [Nginx ingress path mapping](https://kubernetes.github.io/ingress-nginx/user-guide/ingress-path-matching/)


```shell
# Тест должен заработать
[tty0] $ watch -n0.5 curl training-app.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru

[tty1] $ kubectl explain ingress.spec.rules.http.paths
# Выносим приложения на разные пути одного домена
[tty1] $ vi ingress.yml
[tty1] $ kubectl apply -f ingress.yml
```

- [ ] Задание: просмотрите логи всех приложений app-butter. Какие заголовки приходят в приложение?

- [ ] Then участники делятся результатами и соображениями
- Пробовали открыть UI? Всё работает как нужно?
- Как можно исправить проблему с путями? (3 варианта)
- Достаточно ли заголовков которые приходят для восстановления контекста запроса?
- Как посмотреть логи всех инстансов(подов) приложения?
- \* Можно ли настроить TLS для нашего сервиса на ingress? Попробуйте сделать

K8S ConfigMaps
----------------
- [ ] DNS TTL и как это влияет на maintenance приложений
- [ ] Локальность данных при использовании DNS Discovery
- [ ] K8S Config maps - Что это и где стоит использовать а где нет
- [ ] \* Подробно Forwarded RFC + расширения

Hands-on practice quest #09: Redeploy application with custom configuration
---------------------------------------------------------------------------
- [ ] Given пары участников имеют app-knife-deployment и app-butter-deployment в своём namespace
- [ ] When участники запускают команды и применяют новую настройки

- [ ] Задание: Вынести настройку имени сервиса и таймаута app-butter в конфигурацию для возможной смены

```shell
kubectl explain pod.spec.containers.env 
kubectl explain configmap 
vi configmap.yml
vi deployment.yml
kubectl apply -f configmap.yml
kubectl apply -f deployment.yml
```

- [ ] Then Настройки подтянулись в app-knife
- [ ] Задание: измените конфигурацию - сменить таймаут. После проверьте значение
- [ ] Когда меняется значение?

- [ ] Then Настройки подтянулись в app-knife
- [ ] Задание: добавьте в configmap.yml не key-value настройку, а многострочный "файл" (контент ниже). И смонтируйте содержимое в файле в контейнер в папку `/usr/src/app`
```plain
  Content-Type: multipart/form-data; boundary=---------------------------314911788813839

-----------------------------314911788813839
Content-Disposition: form-data; name="foo"

bar
-----------------------------314911788813839
Content-Disposition: form-data; name="baz"

The first line.
The second line.

-----------------------------314911788813839--
```

```shell
kubectl explain pod.spec.volumes
kubectl explain pod.spec.containers.volumeMounts

vi configmap.yml
vi deployment.yml

kubectl apply -f configmap.yml
kubectl apply -f deployment.yml
```

- [ ] Then участники делятся результатами и соображениями
- [ ] Когда меняется значение в файле?
- Как проверить что изменения подтянулись?
- нужен ли рестарт приложения после применения configmap? Обоснуйте так лучше?
- как посмотреть какие сейчас есть балансировщики?
- Можно ли добавить информацию с манифеста приложения в env?

K8S Secrets
----------------
- [ ] K8S Secrets object. Secret types and security considerations
- [ ] Secrets best practice for Cloud Native apps
- [ ] Required support for security from infrastructure side and app side

Hands-on practice quest #10: K8S Secrets simple practice
---------------------------------------------------------------------------
- [ ] Given пары участников имеют app-knife-deployment и app-butter-deployment в своём namespace
- [ ] When участники запускают команды и применяют новую настройки

- [ ] Задание: создать секрет и добавить его в приложения используя секции `data` и `stringData`. Проверить его наличие и формат

```shell
kubectl explain secrets
kubectl explain secrets.data
kubectl explain secrets.stringData
vi handson-10/secret.yml
kubectl apply -f handson-10/secret.yml

# изучим возможности
kubectl explain pod.spec.volumes
kubectl explain pod.spec.containers.volumeMounts
kubectl explain pod.spec.containers.env 
kubectl explain pod.spec.containers.env.valueFrom.secretKeyRef

# Добавляем секреты в наши поды 
vi handson-10/deployment.yml
kubectl apply -f handson-10/deployment.yml
```

- [ ] Then участники делятся результатами и соображениями
- как вывести текущее значение `username` секрета `the-secret-of-tasty-sandwich`? Какое оно и почему?
- можно ли зашифровать секрет?
- чем секрет отличается от ConfigMap
- безопасен ли такой секрет?
- чем отличаются секции data и stringData?
- что нужно сделать чтобы безопасно получать секрет в приложении?
- помните где мы уже сталкивались с секретами?
- что будет если смонтировать секреты во одну директорию?

- [ ] Задание: смонтируйте секрет `butterEndpoint` и любое из значений в `ConfigMap` из предыдущей практики в директорию к приложению - `/usr/src/app`s  

```shell
# изучим возможности
kubectl explain pod.spec.containers.volumeMounts.subPath
kubectl explain pod.spec.volumes.secret.items
kubectl explain pod.spec.volumes.configMap.items

vi handson-10/deployment.yml
```

- [ ] Then участники делятся результатами и соображениями
- Какие возникли сложности при монтировании?
- Попробуйте объяснить почему так сложно?
- Как бы сделали вы?
  
- \* Попробуйте создать секрет другими способами

```shell
kubectl create secret generic secret_name \

# Далее четыре варианта создания секрета из файла
1. --from-file=secret.txt \ 
2. --from-file=key=secret.txt \ 
3. --from-literal=key=value
4. --from-env-file=.env
```

- [ ] Then участники делятся результатами и соображениями по дополнительному заданию
- \* Когда такой способ может быть удобен?

K8S Apps Distribution
------------------------------------------------
- [ ] Manage application topology
- [ ] Taints and Tolerations
- [ ] Topology keys
- [ ] Disruption Budget
- [ ] Node maintenance

Hands-on practice quest #11: Reconfigure apps distribution
----------------------------------------------------------

- [ ] Given пары участников имеют app-knife-deployment и app-butter-deployment в своём namespace
- [ ] When участники запускают команды и применяют новую настройки

- [ ] Задание: Увеличить количество реплик и запустить поды только в двух зонах кластерах

NOTICE: Увеличьте количество реплик до 10, чтобы было более наглядно

WARN: проверьте наличие объекта limitranges (создавался в задание #03). 
Если их нет, то отредактируйте файл `handson/handson-03/default-limits.yml` для установки лимитов в 10Mi. 
После чего примените его если нет с помощью команды:
`kubectl apply -f handson/handson-03/default-limits.yml`.
Это мера предосторожности, чтобы не скушать слишком много ресурсов при увеличении количества реплик


```shell
kubectl edit deployment app-knife-deployment
```

- [ ] Then участники делятся результатами и соображениями
- Всё ли хорошо с приложениями?
- Как вывести доступные зоны?
- Как посмотреть последние свалившиеся приложения?
- Как вывести все ноды на которых запущено приложение `app-knife`?
- Как задать не только дефолтные лимиты на поды, но и общий лимит на весь наш неймспейс? 
- \* Как вывести все зоны на которых запущены приложения `app-knife`?

- [ ] \* Задание: запустить поды на всех узлах кроме одного
- [ ] \* Задание: запустить поды так, чтобы на каждой ноде оказалось по одному app-knife и app-butter.

```shell
kubectl edit deployment app-knife-deployment
```

- [ ] Then участники делятся результатами и соображениями
- Подумайте, насколько это жизненный сценарий?
- \* Задание: Добавьте ResourceQuotas

```shell
kubectl explain resourcequota
vi handson/handson-12/resourcequota.yml
#...
```

K8S Jobs Management and workload distribution
---------------------------------------------
- [ ] Jobs Scheduling и специфичные виды запуска
- [ ] DaemonSets, CronJobs
- [ ] High Level альтернативы. Argo workflow etc.
- [ ] Демо запуск нагрузки на наше приложение через jobs

Hands-on practice quest #12: Practice with DaemonSet and CronJob
----------------------------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress и завершили задания
- [ ] When участники запускают команды и применяют новую настройки

- [ ] Задание: переделайте app-knife `deployment` в `daemonset`. Возьмите за основу ту версию которые сейчас задеплоена

WARN: Это задание выполняется полностью самостоятельно. Без стартового материала
  
```shell
kubectl explain daemonset
vi handson/handson-12/deployment.yml
```

- [ ] Then участники делятся результатами и соображениями
- [ ] Может ли daemonSet `selector.matchLabels` переиспользовать наши старые поды из deployment?
- [ ] Как вы думаете, для чего так сделано?
- [ ] Что имеет смысл запускать с помощью daemonset ?

- [ ] Задание: запустите cronjob которые каждые 5 минут проверяет на работоспособность доступность нашего приложения и печатает результат в консоль

WARN: Это задание выполняется полностью самостоятельно. Без стартового материала

```shell
kubectl explain cronjob
vi handson/handson-12/deployment.yml
```  

- [ ] Then участники делятся результатами и соображениями
- [ ] как что-то сделать при завершение Job ?
- [ ] что будет если предыдущие Job ещё не выполнился, при этом пришло время запускать следующий по расписанию? Как этим управлять?
- [ ] если запускать задание каждые 5 секунд то через 24часа у меня будет больше 17_000 завершённых Job? Как вы думаете, это нормально?
- [ ] а если что-то пошло не так?

K8S Base Homework
-----------------

- [] С помощью полученных ранее знаний разберитесь с NetworkPolicies самостоятельно
- Задание: найдите существующие NetworkPolicies в вашем неймспейсе. Проинтерпретируйте их настройки: 
  - что там за ip?
  - что там за ip? 
  - что это за подсети?
  - что разрешается? 
  - что запрещается?
- Задание: запретить доступ для приложения `app-knife` к приложению `app-butter`
  - С помощью чего блокируется сетевое взаимодействие?

