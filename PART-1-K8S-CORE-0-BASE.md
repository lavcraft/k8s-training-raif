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
    - `ssh`
    - `kubectl`
    - linux shell (`bash` or `zsh`)
    - `curl`
    - `watch`
    - `nslookup/dig`
- [ ] **Демо**. CLI и настройки доступа к кластеру

Hands-on practice quest #00: requisites check and compatibility check
---------------------------------------------------------------------
Проверяем окружение в котором будем работать и настраиваем инструменты для комфортабельной работы
* наличие kubectl - с ним будем работать в течении всего курса, наш основной инструмент
* [optional] docker - нужен/пригодится для сборки демо приложений если хочется эксперементировать с приложениями собственаручно

**Given** сформированы пары участников и в систему установлены все утилиты.
  > С установкой поможет [Инструкция по настройке RHEL подобных систем](HELP-1-K8S-REQUIREMENTS.md)  

**When** участники используют команды и проанализируют их вывод и поведение

```shell script
kubectl

# optional. Need for rebuild demo apps
#docker version
# optional. Need for configure secrets from docker config istead of raw secret creation
#docker login artifactory.raiffeisen.ru
```

```shell script
# выведем "спецификацию" для авто комплита
kubectl completion bash
# применим её
source <(kubectl completion bash)
```

```shell script
ls -la ~/.kube
kubectl cluster-info
```

```shell script
git clone https://gitlabci.raiffeisen.ru/container-trainings/training-k8s
```

**Then** участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Какая версия kubernetes кластера у вас?
- Какая версия kubectl нужна для установленного кластера?
- Какие версии kubectl совместимы с какими версиями кластера?
- Откуда берётся папка `~/.kube` ?
- Есть ли разница в какой оболочке запускать команды? BASH/ZSH/FISH/ETC
- \* Написать config для kubernetes с разными контекстами - prod/dev (смотрят на разные namespace в нашем случае. dev - ваш неймспейс. prod - неймспейс osttok)
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
Настраиваем и проверяем подключение к кластеру K8S

**Given** пары участников  
**When** участники используют команды для извлечения информации из кластера
- **Задача**: узнать список ваших привилегий в кластере

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
kubectl config use-context \tab
kubectl auth can-i --list 
```
:eyes: [Подробнее про права и RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#role-binding-examples)

**Then** участники делятся возникшими и решенными проблемами и отвечают на вопросы
1. Что делать если команда не найдена?
1. Как я авторизовываюсь в кластере?
1. Кто может выдавать права доступа к кластера и в чём заключается "передача"?
1. Можно ли авторизоваться в kubernetes с помощью Ouath 2.0/OIDC т.е через "Соцсеточки"?
1. Как переключать кластеры без use-context?
1. Как избежать ошибок выполнения команд не на "тех" кластерах?
1. Как разделены ресурсы других участников во дном кластере?
1. Как выбрать свой namespace пол умолчанию?
1. **Доп задание**\*: попробуйте использовать несколько конфигураций с помощью переменной окружения `KUBECONFIG`. Что будет с разными кластерами при выводе `kubectl config view`? [О KUBECONFIG](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

## K8S Concepts

- [ ] Declarative vs Imperative
- [ ] Control loop (Observe, Orient, Decide, Act) Jonh Boyd
- [ ] Reconciliation
- [ ] Real control loop examples by kubectl - Desired state vs Current stats (Observe, diff ,act Control model)
- [ ] Kubernetes API-Server and OpenAPI

:shopping_cart: *Материалы*  
  1. [Настрокйка реквизитов docker-registry k8s](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)
  1. [K8S и Private Registry](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)


### Hands-on practice quest #02: I have an application, I wish deploy it to production!
Знакомимся с базовыми возможностями kubectl - запускаем простейшую работу в кластере и ловим типичные проблемы

**Given** пары участников прошедших Docker тренинг  
**When** участники собирают приложение и деплоят в кластер
```shell script
kubectl create job test --image=busybox -- echo "Hello World"
```

**Задание**: Понять сработала ли наша задача
1. Почему не вывелись логи?
1. Почему у пода такое странное имя?

```shell script
kubectl describe pod test-<id>

kubectl create job test --image=artifactory.raiffeisen.ru/ext-techimage-docker/busybox -- echo "Hello World" 

kubectl get pods
kubectl get jobs

kubectl logs jobs/test
kubectl delete job test
kubectl get pods
```

**Then**: участники делятся мыслями и отвечают на вопросы
1. в чём различие в семантике написания команд - `kubectl get job test` и `kubectl get jobs/test`? Попробуйте объяснить
1. что будет если команду вывода логов разными способами?
1. удаляется ли созданный pod после удаления job test? 

**Задание**: запустить образ с curl, dig прочими инструментами для дебага

```shell script
# Попробуем запустить образ из репозиторя, требущего авторизацию
# INFO: Пригодится нам в будущем для исследовательских целей
kubectl run -it debug --image=artifactory.raiffeisen.ru/ext-rbru-container-community-docker/cli-tools -- /bin/sh
kubectl get events
kubectl create secret docker-registry regcred --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword>
# <your-registry-server> - например https://artifactory.raiffeisen.ru
kubectl explain serviceaccount.imagePullSecrets
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "regcred"}]}'

kubectl run -it debug --image=artifactory.raiffeisen.ru/ext-rbru-container-community-docker/cli-tools -- /bin/sh

kubectl get pods
```

**Then** участники делятся мыслями и соображениями
1. куда нужно запушить образ чтобы kubernetes кластер его успешно спулил?
1. какой правильный процесс деплоя образов?
1. чем отличается Job от Pods?
1. когда удаляется Pod созданный при создании Job?
1. кто ответственнен за это удаление?
1. **Доп задание**\*: Как упростить процесс создания `regcred` секрета если вы уже залогинены в docker registry через docker login?
1. **Доп задание**\*: Отредактируйте `serviceaccount` не через patch а через `kubectl edit`. Что будет если ошибиться в редактируемом манифесте?
1. **Доп задание**\*: запустить под с лимитами по памяти в 16mb. [About K8S Limits](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)

## K8S Container isolation

- [ ] Clusters and nodes
- [ ] Clusters network isolation
- [ ] Application in cluster resources
- [ ] Cluster network availability
- [ ] Docker run in Kubernetes and minimal configuration. Namespaces and pods fast introduction
- [ ] Демо kubectl apply/get/describe и output моды

:shopping_cart: *Материалы*  
1. [Подробно о ресурсах и CGroups](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html-single/resource_management_guide/index)
1. [Про установки лимитов](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)

### Hands-on practice quest #03: Run apps in cluster
Познаём различные ограничения (технические и концептуальные) контейнерных технологий в связке с абстракциями K8S (Pod). 
Делаем это на примере демо приложения "нож" и "масло"

**Given** пары участников имеют собранные образы   приложений
**When** участники описывают поды с двумя сервисами и меняют состояние kubernetes


```shell script
cat handson/handson-03/apps-01.yml
kubectl apply -f handson/handson-03/apps-01.yml
```

**Then** участники делятся результатами и соображениями
- Какой статус пода?
- Найдите ошибку
- Как посмотреть логи конкретного контейнера пода?
**Задание**: Исправьте ошибку пользуясь информацией `kubectl explain`

```shell script
# INFO: Не забываем что можно указать дефолтный контекст
kubectl config set-context --current --namespace=<>
kubectl explain pod.spec
kubectl edit butter-fabric
```

**Then**: участники делятся наблюдениями и разбираются что пошло не так
- Какие возникли трудности при редактировании? Объясните почему так?
**Задание**: передеплоить приложения отдельно

```shell script
kubectl delete -f handson/handson-03/apps-01.yml
kubectl apply -f handson/handson-03/apps-02.yml
kubectl explain pod.spec.containers.resources
# add limits 8Mi и cpu 200m
kubectl edit pod app-butter
vi handson/handson-03/apps-02.yml
kubectl apply -f handson/handson-03/apps-02.yml
kubectl get pods
```
:eyes: [Про установки лимитов](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)

**Then** приложение не запуcтилось
- Найти информацию для извлечения признака проблемы
- Найти оптимальное значение лимитов и исправить
- Вывести все поды с ошибочным статусом в формате `name/image - status`. Смотри документацию jsonpath/go-template [kubectl jsonpath doc](https://kubernetes.io/docs/reference/kubectl/jsonpath/)
- **Доп задание**\*: Задайте дефолтные квоты для приложений в вашем namespace


**Then** участники делятся результатами и соображениями
1. Как узнать что там с нашим приложением и результатами выполнения команды?
1. Если другой задеплоит под с таким же названием, он перезатрёт мой?
1. А в какой namespace приложение задеплоилось?
1. А на каком узле запустилось?
1. Как узнать какие проблемы у приложения при запуске?
1. Что значат ограничения по CPU 200mi ? Это сколько?
1. как понять какие ресурсы доступны для меня?
1. как понять какие ресурсы работают в рамках неймспейса??
1. как получить сырой вывод api?
1. получилось ли у вас отредактировать лимиты приложения? Какие были трудности?
1. **Доп задание**\*: Как приложение понимает что ему нужно выключаться? Кто быстрее выключается, `app-knife` или `app-butter`?

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
Получаем доступ к нашим приложениям, исследуем особенности сетевого взаимодействия. 
Стараемся обращать внимание на жизненный цикл наших приложений

**Given** пары участников имеют задеплоенную версию   приложений
**When** участники запускают команды и применяют новую настройки
**Задание**: Запустить cli-tools если отсутствует

> Для выполнения задания требуются приложения из [handson #03](#hands-on-practice-quest-03-run-apps-in-cluster)

```shell
# проверим что есть под debug, иначе запустим (см handson #02)
kubectl get pod debug 
# Откроем новую консоль
# Зайдём в контейнер и будем него будем производить дебаг наших приложений
kubectl exec -it debug -- /bin/bash
...
```

1. **Задание**: С помощью материалов ниже вычислите IP адресс и имя запущенного `Pod` и сделать запрос к сервису `app-knife`
    > Материалы - [Debugging DNS Resolution](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
    > и [DNS Pod Service](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pods)
1. **Задание**: Допишете сервис для недостающего приложения и повторите запрос к app-knife

```shell
cat handson/handson-04/services.yml
kubectl explain service.spec
kubectl expose --help
# применяем их
kubectl apply -f  handson/handson-04/services.yml
kubectl get services
kubectl get endpoints
```

**Задание**: Проверить работоспособность сервиса? ([О метаданных и labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#motivation))
1. Кто быстрей удаляется при `kubectl delete pod app-knife/app-butter`? Подумайте почему?
1. Работает ли `kubectl port-forward pod/app-knife 8080` если не задан `containerPort`?
1. На что влияет containerPort в pod.spec.containers.ports.containerPort?
1. Что будет если при работающей команде `kubectl port-forward pod/app-knife 8080` перезапустить pod app-knife?

**Задание**: Заставить сервис `app-butter-service` возвращать json ответ. (Совет: попробуйте разобраться, отвечая на ответы дальше по списку)
1. Объясните ответы от сервиса `app-knife` в случаях: 
    1. не сервиса `app-butter-service`.
    1. есть сервис `app-butter-service` но нет labels у `app-butter`. (Воспользуйтесб debug подом и посмотрите что возвращает `app-butter-service`)
    1. есть и сервис и соответсвующий ему `labels.app`? (Попробуйте воспользоваться командой `kubectl edit pod app-butter` и добавить `metadata.labels.app=app-butter`
1. Можно ли включить интерактивный шел в запущенном контейнере?
1. Как выполнить команду в запущенном контейнере не заходя в него?

Попробуйте выполнить в debug контейнере следующие команды:
```shell
kubectl get pods
[debug] $ nslookup <pod-ip>
[debug] $ nslookup <pod-name>
[debug] $ nslookup <service-ip>
[debug] $ nslookup <service-name>
```

**Then** nslookup выдал обратные зоны по запрашиваемым IP
1. Кто ответственнен за это преобразование?
1. Как найти кто ответственен?
1. Можно ли его переконфигурировать?
1. **Задание**: Найти кто влияет на название DNS имени

[man resolv.conf](https://man7.org/linux/man-pages/man5/resolv.conf.5.html)

```shell script
[debug] $ cat /etc/resolv.conf
kubectl get services --all-namespaces | grep <nameserver-ip>
kubectl get pods --namespace=kube-system -l k8s-app=kube-dns
```

  
**Then** участники делятся результатами и соображениями. Контрольные вопросы блока:
1. Как узнать что сервис настроен правильно и работает корректно?
1. Рестартовало ли приложение после повторного `kubectl apply -f pod.yml` и почему?
1. Что будет если изменить `metadata.labels.app` ?
1. Что если остановить приложение и обратиться к сервису?
1. Кто настраивает `kube-dns` ?
1. Как диагнастировать `kube-dns` ?

K8S Internal and External access to containers
----------------------------------------------
- [ ] Containers Shell
- [ ] K8S Ingress и его описание
- [ ] kubectl exec
- [ ] [Подробнее об ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

Hands-on practice quest #05: Redeploy application with ingress
--------------------------------------------------------------
Анонсируем доступ к нашему приложению из вне кластера. Подымаем вопросы доступности приложений и учимся искать проблему с помощью изученных ранее команд

1. **Given** пары участников имеют задеплоенную версию   приложений и сервисов
1. **When** участники запускают команды и применяют новую настройки
1. **Задание**: Настроить доступ из сети банка к приложению следуя инструкциям

```shell script
# тест должен заработать
watch -n0.1 curl -s -v http://app-knife-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
kubectl explain ingress.spec
# Завершите ingress конфигурацию
vi handson/handson-05/ingress.yml
kubectl apply -f handson/handson-05/ingress.yml
```

**Then** участники делятся результатами и соображениями
1. Как теперь достучаться до сервиса?
1. Что будет если остановить сервис?
1. Что будет если сервиса на который ссылаемся в ingress не будет?
1. Что случится если его добавить во время работы? Почему?
1. Как попробовать посмотреть что внутри?
1. Сколько инстансов каждого приложения?
1. Как ingress связан с ingress-controller?
1. Где находится ingress-controller?
1. Кто отвечает за "анонсирование" внутренних имён сервисов и имён для ingress?
1. **Доп задание**\*: Как посмотреть справку по конкретной версии API с помощью `kubectl explain ingress`?
1. **Доп задание**\*: Перепишите ingress с beta версии API на релизную - `networking.k8s.io/v1`
1. **Доп задание**\*: Можно ли отправить запрос на ingress, DNS которого не резолвится? Если да то как?

K8S Namespace, Pods, Containers again and Scaling
-------------------------------------------------
- [ ] ReplicaSet
- [ ] Deployment
- [ ] TopologyKey

Hands-on practice quest #06: Redeploy application with replicas
---------------------------------------------------------------
Изучаем подробнее вопрос доступности приложений и маршрутизации трафика с учётом особенностей жизнненного цикла приложений

**Given** пары участников имеют задеплоенную версию   приложений и сервисов и ingress
**When** участники запускают команды и применяют новую настройки
**Задание**: изменить запуск приложений в поде на запуск c помощью deployment (для app-butter и app-knife)

```shell
# запускаем тестовую команду. Видим что падает
[tty0] $ watch -e -n0.1 curl --fail -s -v app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
[tty1] kubectl explain deployment
# применяем деплоймент
## смотрим шаблон
[tty1] $ cat handson/handson-06/deployment.yml
## заполняем недостающую секцию
[tty1] $ vi handson/handson-06/deployment.yml
[tty1] $ kubectl apply -f handson/handson-06/deployment.yml
# после запуска снова запускаем команду для проверки
[tty0] $ watch -e -n0.1 curl --fail -s -v app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru
```

> Флаг --fail команды curl завершает команду с ошибочным статусом если был HTTP ответ не 200

> Флаги -s и -v команды curl носят информационный характер и делают более детальный вывод

> Флаг -e для команды watch прерывает повторы команды если команда завершилась ошибкой


**Задание**: Изменить количество реплик для deployment app-butter-deployment на 3 с помощью команды `kubectl scale` и исследовать надёжность нашего деплоймента
    > Проверить работспособность можно командой `watch -e -n0.1 curl --fail -s -v app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru`

**Then** тест упал при увеличении количества реплик когда сервис вернул HTTP 500
1. Почему упал тест? Разберитесь в источнике проблемы
1. Участники делятся результатами и соображениями
1. Как узнать в какое приложение попал запрос?
1. Как посмотреть логи сразу со всех инстансов?
1. Как распределены инстансы приложений?
1. В какой момент приложение становится доступно для запроса curl?
1. Как вы думаете, какой вид балансировки используется при вызове app-butter из app-knife?
1. **Доп задание**\*: чтобы понять на каких узлах ваше приложение, выведите его в формате имя/узел

K8S Application Probes and full deployments
-------------------------------------------
- [ ] liveness and readiness probess
- [ ] Application access model
- [ ] LB probes and application availability for user corner cases
- [ ] K8S Deploments

Hands-on practice quest #07: Redeploy application with probes
-------------------------------------------------------------
Пытаемся митигировать недоступность нашего приложения средствами K8S

**Given** пары участников имеют задеплоенную версию   приложений и сервисов и ingress
**When** участники запускают команды и применяют новую настройки

[Kubernetes probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

Конфигурируем для одного приложение rediness/liveness для другого нет
```shell
[tty0] $ watch -e -n0.1 curl --fail -s -v app-butter-ingress.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru

[tty1] $ kubectl edit deployment app-butter-deployment
[tty1] $ kubectl apply -f deployment.yml
```

**Then** участники делятся результатами и соображениями
1. Будут ли доходить запросы до приложения если livenessProbe отрицательна а readinessProbe положительна?
1. Достаточно ли только readiness для надёжной работы app-butter?
1. В каком случае нельзя без `startupProbe`?
1. Как связаны `startupProbe` и `livenessProbe`, `readinessProbe`?
1. Можно ли поймать такой же эффект как 500 ошибка при старте, но при остановке приложения?
1. Доходят ли запросы через Service если Pod в статусе Terminating?
1. **Доп задание**\*: HAL API: как быть с обратными ссылками? Как узнать с какого адреса пришёл запрос?

Hands-on practice quest #7.1: Edit deployment
---------------------------------------------
Разбираемся с особенностями работы Pod созданных через Deployment\*

**Given** пары участников имеют задеплоенную версию   приложений и сервисов и ingress
**When** участники запускают команды и применяют новую настройки

Изменим `metadata.labels.app` на `app-knife-edited`
```shell
[tty0] $ kubectl get replicasets -w
[tty1] $ kubectl edit pod app-butter-<custom-id-autocomplete-it>
[tty1] $ kubectl get pods
```

Вернём обратно metadata.labels.app
```shell
kubectl edit pod app-butter-<>
kubectl get pods
```

**Then** участники делятся результатами и соображениями
1. Что случилось со старым приложением при изменении labels?
1. Объясните поведение
1. Как посмотреть текущие ReplicaSet?
1. Как понять привязан ли он к Deployment?
1. Будет ли работать replica set?
1. Как удалить все изменённые поды?

K8S Deployment rollout
----------------------
- [ ] Отличия Deployment от ReplicaSet и рекомендации
- [ ] kubectl rollout undo demo

Hands-on practice quest #07.2: deployment rollout
-------------------------------------------------
Исследуем механизмы встроенного версионирования и отката ревизий в K8S

**Given** пары участников имеют задеплоенную версию   приложений и сервисов и ingress
**When** участники запускают команды и применяют новую настройки

Изменим metadata.labels.app
```shell
[tty0] $ kubectl get rs -w # rs = replicaset
[tty1] $ kubectl rollout history deployment app-butter-deployment
[tty1] $ kubectl rollout undo deployment app-butter-deployment --to-revision=<revision-number>
[tty1] $ kubectl get deployment app-butter-deployment -o yaml
[tty1] $ kubectl get rs
```

**Then** участники делятся результатами и соображениями
1. Можно ли откатиться на уже откаченную версию (последнюю)?
1. Как посмотреть все deployments на кластере?
1. Изменится ли версия deployment если изменить связанный с ним `ReplicaSet`? А если Pod?
1. Почему в списке `ReplicaSet` есть какие то пустые? Объясните зачем они

K8S Multi path applications
---------------------------
- [ ] Basic Reverse proxy concepts
- [ ] Ingress rules - rewrite, split locations, certs

Hands-on practice quest #08: Two apps in one domain
---------------------------------------------------
Изучаем особенности маршрутизации трафика в кластер и настраиваем более сложную схему. 
Подымаем вопросы совместимости и Reverse Proxy компромиссы

**Given** пары участников имеют задеплоенную версию   приложений и сервисов и ingress
**When** участники запускают команды и применяют новую настройки
**Задание**: сделать новый ingress, чтобы приложения работали на одном хосте

> [Nginx ingress docs](https://kubernetes.io/docs/concepts/services-networking/ingress/)

> [Nginx ingress path mapping](https://kubernetes.github.io/ingress-nginx/user-guide/ingress-path-matching/)


```shell
# Тест должен заработать
[tty0] $ watch -n0.5 curl training-app.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru/app-knife/
# or
[tty0] $ watch -n0.5 curl training-app.<namespace-name>.lb.<cluster-name>.k8s.raiffeisen.ru/app-butter/

[tty1] $ kubectl explain ingress.spec.rules.http.paths
# Выносим приложения на разные пути одного домена
[tty1] $ vi ingress.yml
[tty1] $ kubectl apply -f ingress.yml
```

**Задание**: просмотрите логи всех приложений `app-butter`
1. Какие заголовки приходят в приложение?
1. Как посмотреть логи всех подов деплоймента?
1. Как ещё можно отфильтровать приложения чтобы выводить логи группы подов?

**Then** участники делятся результатами и соображениями
1. Пробовали открыть UI? Всё работает как нужно?
1. Как можно исправить проблему с путями? (3 варианта)
1. Достаточно ли заголовков которые приходят для восстановления контекста запроса?
1. Как посмотреть логи всех инстансов(подов) приложения?
1. как посмотреть какие сейчас есть "балансировщики" в кластере?
1. **Доп задание**\* Можно ли настроить TLS для нашего сервиса на ingress? Попробуйте сделать

K8S ConfigMaps
----------------
- [ ] DNS TTL и как это влияет на maintenance приложений
- [ ] Локальность данных при использовании DNS Discovery
- [ ] K8S Config maps - Что это и где стоит использовать а где нет
- [ ] \* Подробно Forwarded RFC + расширения

Hands-on practice quest #09: Redeploy application with custom configuration
---------------------------------------------------------------------------
Знакомимся с механизмами внешних конфигураций подробно и пытаемся сконфигурировать наши приложения
Подымаем вопросы конфигурации "коробочных" приложений

[] **Given** пары участников имеют   app-knife-deployment и app-butter-deployment в своём namespace
[] **When** участники запускают команды и применяют новую настройки
[] **Задание**: Вынести настройку имени сервиса (CALL_HOST) и таймаута app-butter (CALL_TIMEOUT) в конфигурацию для возможной смены

[Подробнее о ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/)

```shell
kubectl explain pod.spec.containers.env 
kubectl explain configmap 
vi configmap.yml
vi deployment.yml
kubectl apply -f configmap.yml
kubectl create configmap -h 
```
**Then** ConfigMap успешно создался и вы убедились что он создан
1. **Задание**: Попробуйте вывести ключи сохраннёные внутри ConfigMap
1. В каком формате хранятся данные внутри ConfigMap?
1. Как посмотреть что было сохранено и почему так сложно? Поясните

[Про Shell ENV Variables](https://en.wikipedia.org/wiki/Environment_variable)

**Then** Настройки подтянулись в app-knife
1. **Задание**: измените конфигурацию - сменить таймаут. После проверьте значение
1. Когда меняется значение?
1. **Then** Настройки подтянулись в app-knife
1. **Задание**: добавьте в configmap.yml не key-value настройку `nginx.conf`, а многострочный "файл" (контент ниже). И смонтируйте содержимое в файле в контейнер в папку `/usr/src/app`

```plain
user       www www;
error_log  logs/error.log;

http {
  include    conf/mime.types;
  index    index.html index.htm index.php;

  server { # php/fastcgi
    listen       80;
    server_name  example.com www.example.com;
    
    location ~ \.php$ {
      fastcgi_pass   127.0.0.1:1025;
    }
  }
}
```

```shell
kubectl explain pod.spec.volumes
kubectl explain pod.spec.containers.volumeMounts

vi configmap.yml
vi deployment.yml

kubectl apply -f configmap.yml
kubectl apply -f deployment.yml
```

**Then** участники делятся результатами и соображениями
1. Когда меняется значение в файле?
1. Как проверить что изменения подтянулись?
1. нужен ли рестарт приложения после применения configmap? Обоснуйте так лучше?
1. Можно ли добавить информацию с манифеста приложения в env?

[Exposing information to pod](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/)

K8S Secrets
----------------
- [ ] K8S Secrets object. Secret types and security considerations
- [ ] Secrets best practice for Cloud Native apps
- [ ] Required support for security from infrastructure side and app side

Hands-on practice quest #10: K8S Secrets simple practice
--------------------------------------------------------
Знакомимся с секретами K8S, обсуждаем их компромисы и варианты использования
Пробуем сконфигурировать наше приложение с помощью секретов

**Given** пары участников имеют `app-knife-deployment`   и `app-butter-deployment` в своём namespace
**When** участники запускают команды и применяют новую настройки
1. **Задание**: создать секрет и добавить его в приложения используя секции `data` и `stringData`. Проверить его наличие и формат
1. **Задание**: смонтировать секреты `username` и `password` в папку `/usr/src/app` вместе с конфигом `nginx.conf` из предыдущего задания

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

**Then** участники делятся результатами и соображениями
1. как вывести текущее значение `username` и `password` секрета `the-secret-of-tasty-sandwich`? Какое оно и почему?
1. можно ли зашифровать секрет?
1. чем секрет отличается от ConfigMap
1. безопасен ли такой секрет?
1. чем отличаются секции data и stringData?
1. что нужно сделать чтобы безопасно получать секрет в приложении?
1. помните где мы уже сталкивались с секретами?
1. что будет если смонтировать секреты во одну директорию?

**Задание**: смонтируйте секрет `nginx.conf` и любое из значений в `ConfigMap` из предыдущей практики в директорию к приложению - `/usr/src/app`s  

```shell
# изучим возможности
kubectl explain pod.spec.containers.volumeMounts.subPath
kubectl explain pod.spec.volumes.secret.items
kubectl explain pod.spec.volumes.configMap.items

vi handson-10/deployment.yml
```

**Then** участники делятся результатами и соображениями
1. Какие возникли сложности при монтировании?
1. Попробуйте объяснить почему так сложно?
1. Как бы сделали вы?
1. **Доп задание**\*: Попробуйте создать секрет другими способами

```shell
kubectl create secret generic secret_name \

# Далее четыре варианта создания секрета из файла
1. --from-file=secret.txt \ 
2. --from-file=key=secret.txt \ 
3. --from-literal=key=value
4. --from-env-file=.env
```

**Then** участники делятся результатами и соображениями по дополнительному заданию
1. В чём отличие типа у regcred от обычного секрета? Как закодирован ваш пароль?
1. **Доп задание**\*: Когда такой способ может быть удобен?
1. **Доп задание**\*: Задание: попробуйте найти свой пароль из секрета regcred ( создавался в [задании 02](#hands-on-practice-quest-02-i-have-an-application-i-wish-deploy-it-to-production) )

K8S Apps Distribution
---------------------
- [ ] Manage application topology
- [ ] Taints and Tolerations
- [ ] Topology keys
- [ ] Disruption Budget
- [ ] Node maintenance

Hands-on practice quest #11: Reconfigure apps distribution
----------------------------------------------------------
Знакомимся с техниками управления распределением Pod по Node в кластере K8S. Перераспределяем поды наших приложений по заданным правилам

**Given** пары участников имеют `app-knife-deployment`   и `app-butter-deployment` в своём namespace
**When** участники запускают команды и применяют новую настройки
**Задание**: Увеличить количество реплик и запустить поды только в двух зонах кластерах. :eyes: Увеличьте количество реплик до 10, чтобы было более наглядно

> проверьте наличие объекта limitranges (создавался в задание #03). [Подробнее о практиках и ограничениях масштабирования](https://kubernetes.io/docs/setup/best-practices/cluster-large/)
> Если их нет, то отредактируйте файл `handson/handson-03/default-limits.yml` для установки лимитов в 10Mi. 
> После чего примените его если нет с помощью команды:
`kubectl apply -f handson/handson-03/default-limits.yml`.
> :warning: Это мера предосторожности, чтобы не скушать слишком много ресурсов при увеличении количества реплик


```shell
kubectl edit deployment app-knife-deployment
```

**Then** участники делятся результатами и соображениями
1. Всё ли хорошо с приложениями?
1. Как вывести доступные зоны?
1. Как посмотреть последние свалившиеся приложения?
1. Как вывести все ноды на которых запущено приложение `app-knife`?
1. Как задать не только дефолтные лимиты на поды, но и общий лимит на весь наш неймспейс? 

**Доп задание**\*: Как вывести все зоны на которых запущены приложения `app-knife`?
**Доп задание**\*: запустить поды на всех узлах кроме одного
**Доп задание**\*: запустить поды так, чтобы на каждой ноде оказалось по одному app-knife и app-butter.
1. Какую роль играет `topologyKey` для `requiredDuringSchedulingIgnoredDuringExecution`?
1. Можно ли сделать так, чтобы app-knife деплоились только на узлы на которых нет app-butter?
1. Для чего используется anti affinity правила?
  > [Подробнее про Affinity и способы распределения Pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)

```shell
kubectl edit deployment app-knife-deployment
```

**Then** участники делятся результатами и соображениями
1. Подумайте, насколько это жизненный сценарий?
**Доп задание**\*: Изучите объект `ResourceQuotas` и добавьте его. Как проверить его работоспособность?
**Доп задание**\*: Изучите объект `PodDisruptionBudget` и добавьте его. Как проверить его работоспособность?

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
Знакомимся с новыми абстракциями K8S для запуска новых видов работ

**Given** пары участников имеют задеплоенную версию   приложений и сервисов и ingress и завершили задания
**When** участники запускают команды и применяют новую настройки
**Задание**: переделайте app-knife `deployment` в `daemonset`. Возьмите за основу ту версию которые сейчас задеплоена

:eyes: Это задание выполняется полностью самостоятельно. Без стартового материала
  
```shell
kubectl explain daemonset
vi handson/handson-12/deployment.yml
```

**Then** участники делятся результатами и соображениями
1. Может ли daemonSet `selector.matchLabels` переиспользовать наши старые поды из deployment?
1. Как вы думаете, для чего так сделано?
1. Что имеет смысл запускать с помощью daemonset ?

1. Задание: запустите cronjob которые каждые 5 минут проверяет на работоспособность доступность нашего приложения и печатает результат в консоль

:eyes: Это задание выполняется полностью самостоятельно. Без стартового материала

```shell
kubectl explain cronjob
vi handson/handson-12/deployment.yml
```  

**Then** участники делятся результатами и соображениями
1. как что-то сделать при завершение Job ?
1. что будет если предыдущие Job ещё не выполнился, при этом пришло время запускать следующий по расписанию? Как этим управлять?
1. если запускать задание каждые 5 секунд то через 24часа у меня будет больше 17_000 завершённых Job? Как вы думаете, это нормально?
1. может ли CronJob запустить много Job одновременно? (`kubectl explain cronjob.spec.startingDeadlineSeconds`)
1. а если что-то пошло не так?

[Про ограничения и неоднозначные моменты CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-job-limitations)

K8S Base Homework
-----------------

**Задание**: С помощью полученных ранее знаний разберитесь с NetworkPolicies самостоятельно
**Задание**: найдите существующие NetworkPolicies или попробуйте создать новые в вашем неймспейсе. Проинтерпретируйте их настройки: 
  - что там за ip?
  - что там за ip? 
  - что это за подсети?
  - что разрешается? 
  - что запрещается?
**Задание**: запретить доступ для приложения *app-knife* к приложению *app-butter*
  - С помощью чего блокируется сетевое взаимодействие?
  - Гарантируется ли что во всех случаях запросы от *app-knife* к *app-butter* будут заблокированы?



