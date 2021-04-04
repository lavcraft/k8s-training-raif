Intro to Docker
===============
8 часов, 2 ак. дня.

Prerequisites
=============
- [ ] RAM ≥ 4Gb
- [ ] RHEL8
- [ ] Права локального админа для аккаунта участника {{ account }}
- [ ] Доступен корпоративный Docker {{ registry-host }} `artifactory.raiffeisen.ru`
- [ ] Доступен корпоративный Docker {{ images-registry }} с производственными образами `{{ registry-host }}/ext-rbru-techimage-docker`
- [ ] Доступен корпоративный Docker {{ project-registry }} учебного проекта `{{ registry-host }}/training-docker`
- [ ] Доступен дистрибутив рабочего приложения `{{ project-registry }}/dbo-1.0-SNAPSHOT.jar`
#TODO ^
- [ ] Доступен исходный проект рабочего приложения `{{ project-registry }}/dbo-1.0-SNAPSHOT-src.jar`
#TODO ^
- [ ] Доступен необходимый компонент рабочего приложения `{{ registry-host }}/com/github/tomakehurst/wiremock-standalone/2.9.0/wiremock-standalone-2.9.0.jar`
#TODO ^
- [ ] Установлен DockerCE
```shell
dnf install -y docker-ce
```

Agenda
======
Введение в Docker
-----------------
- [ ] Зачем нужен Docker? Функции.
- [ ] Метафора морского контейнера
- [ ] Виртуализация или контейнеризация?
- [ ] Процесс ОС "на стероидах": какие ресурсы необходимо изолировать и инкапсулировать?
- [ ] Как устроен Docker? [Элементы](http://alexander.holbreich.org/docker-components-explained/).
- контейнеризованное приложение
- [dockercli](https://docs.docker.com/engine/reference/commandline/cli/)
- [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)
- [containerd](https://www.docker.com/blog/what-is-containerd-runtime/)
- [runc](https://www.threatstack.com/blog/diving-deeper-into-runtimes-kubernetes-cri-and-shims)
- disk image provisioning tool (dockercli) and language: Dockerfile
- disk image
- Docker image registries: [docker hub](http://hub.docker.com) and corporate registries
- container = process + container data (container layer)
- [ ] Registry [authentication and credentials storing](https://docs.docker.com/engine/reference/commandline/login/)

Hands-on practice quest #00: prerequisites sound-check
---------------------------
- [ ] Given 
- сделан форк данного руководства на github для собственных пометок
- проставлены закладки на основные ресурсы раздела [Prerequisites](#Prerequisites)
- сформированы пары участников
- Hint: чередование ролей в паре 
- Hint: синонимы команд docker cli
- Hint: `... --help`


- [ ] When участники именуют сценарии, выполняют команды и анализируют их вывод и поведение

- Сценарий "Как ...?"
```shell
docker version # TODO: собственные пометки участников для будущего использования в проектах
docker system info
docker system df
cat $HOME/.docker/config.json
cat $HOME/.docker/daemon.json
docker events
```

- Сценарий "Как ...?" (в новом shell, чтобы параллельно видеть вывод `docker events`)
```shell
docker logout
docker login {{ registry-host }}
```

- Сценарий "Как ...?"
```shell
docker pull {{ images-registry }}/postgres:11-alpine
docker system df
````

- Сценарий "Как ...?"
```shell
docker container ls [--all]
docker run --name demo -it {{ images-registry }}/alpine /bin/sh #<-TODO image name
/# cat /etc/os-release
/# exit 
```

- Сценарий "Как ...?"
```shell
docker container ls [--all]
docker rm [-f] demo
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- Успешна ли сконфигурирована система для использования docker?
- Какая версия API?
- Откуда взялся образ диска?
- Сколько места занимает образ?
- Сколько места занимает контейнер?

Жизненный цикл готового образа
------------------------------
- [ ] Задача среды исполнения контейнеров: изоляция диска
- [ ] Отображение диска контейнера на диск хостовой системы: образ
- [ ] [Хранение образа на хостовой системе](https://docs.docker.com/storage/storagedriver/select-storage-driver/)
- [ ] [Прием copy-on-write и OverlayFS](https://docs.docker.com/storage/storagedriver/)
- [ ] Что должно быть на диске для запуска и работы контейнеризованного приложения?
- [ ] Состав образа диска (от `scratch` до prod-ready)
- [ ] Жизненный цикл образа в репозитории и аналогии с git
- [ ] Идентификация образов: id, репозиторий, имя, теги
- [ ] Semantic versioning vs unique tags

Hands-on practice quest #01: pre-built disk image lifecycle
---------------------------
- [ ] Given пары участников

  
- [ ] When участники именуют сценарии, выполняют команды и анализируют их вывод и поведение
- Сценарий "Как ...?"
```shell
docker image ls # TODO: собственные пометки участников для будущего использования в проектах
```

- Сценарий "Как ...?"
```shell
docker pull {{ images-registry }}/alpine
docker image ls
```

- Сценарий "Как ...?"
```shell
docker image history {{ images-registry }}/alpine
docker image inspect {{ images-registry }}/alpine
```

- Сценарий "Как ...?"
```shell
docker run --name demo -it {{ images-registry }}/alpine
/# touch side-effect
/# exit
docker container commit demo training-docker/demo
docker image ls
```

- Сценарий "Как ...?"
```shell
docker image tag training-docker/demo:latest training-docker/demo:1.0.0
docker image ls
```

- Сценарий "Как ...?"
```shell
docker image push training-docker/demo:1.0.0
```

- Сценарий "Как ...?"
```shell
docker image prune
docker image rm training-docker/demo:1.0.0
docker image ls
docker image rm training-docker/demo:latest
docker image ls
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- В чем физический смысл удаления образа?

Жизненный цикл контейнера
-------------------------
- [ ] container = process + container data (container layer)
- [ ] Какие ресурсы хоста нужно выделить для запуска контейнера?
- [ ] Экстернализация конфигурации приложения при запуске контейнера
- [ ] [Запуск контейнера](https://docs.docker.com/engine/reference/run/#detached--d): run vs start
- [ ] Именование контейнера
- [ ] Ключевые параметры запуска контейнера
- [ ] Просмотр активных контейнеров: работающих и остановленных
- [ ] Подключение к рабочему контейнеру
- [ ] Остановка контейнера
- [ ] Удаление контейнера и его физический смыл
- [ ] Журнал контейнера

Hands-on practice quest #02: container lifecycle
---------------------------
docker [container] create
docker [container] rename
docker [container] attach
docker [container] commit
docker [container] cp
docker [container] diff
docker [container] exec
docker [container] export
docker [container] inspect
docker [container] port
docker [container] kill
docker [container] ls [--format '{{.ID}} | {{.Names}} | {{.Status}} | {{.Image}}'] 
docker [container] pause
docker [container] unpause
docker [container] prune
docker [container] restart
docker [container] rm
docker [container] run
docker [container] start
docker [container] stop
docker [container] top
docker [container] logs

Контейнеризация простого сервиса: образ "с нуля"
--------------------------------
- [ ] Что необходимо сконфигурировать для создания образа диска с простым сервисом?
- [ ] Экстернализация конфигурации приложения при сборке образа
- [ ] [Команда сборки образа](https://docs.docker.com/engine/reference/commandline/build/#tag-an-image--t) и роль Dockerfile
- [ ] Структура Dockerfile и его декларативность
- [ ] Ключевые директивы
- [`FROM`](https://docs.docker.com/engine/reference/builder/#from)
- `ADD` vs `COPY` (+ `.dockerignore`)
- `RUN`
- `ENV`
- `ARG`
- `EXPOSE`
- `VOLUME`
- `ENTRYPOINT` vs `CMD` (+ `shell` vs `exec` formats)
- `LABEL`

Hands-on practice quest #03: _simple_ application containerization
---------------------------
docker [builder] build
docker [image] build 
docker [builder] prune

```shell
cd /docker-application/application/backend
docker build --tag {{ project-registry }}/{{ account }}-backend:1.0.0 .
docker run --rm --detach --publish 8080:8080 --name backend --env SPRING_PROFILES_ACTIVE=qa --volume $(pwd)/log:/dbo/log {{ project-registry }}/{{ account }}-backend:1.0.0
```

Введение в контейнеризацию составного приложения
------------------------------------------------
- [ ] Что нужно для целостной работы multi-container приложения?
- Целостная сборка образов (опционально)
- Целостный запуск, работа и завершение
- [ ] Оркестраторы:  `compose`, `swarm`, `k8s` и их ограничения
- [ ] Клиенты для API орекстраторов: Docker Compose (±Dockerfile) и Docker Stack (over Swarm)
- [ ] `docker-compose.yml`
- [ ] Какие ресурсы необходимо виртуализировать?
- узлы
- [network](#Виртуализация сети)
[Docker configs](https://docs.docker.com/engine/swarm/configs/)
[Docker secrets](https://docs.docker.com/engine/swarm/secrets/)

Hands-on practice quest #04: _multi-component_ application containerization
---------------------------
```shell
cd /docker-application/application

nano proxy/nginx.conf #TODOs
docker build --tag {{ project-registry }}/{{ account }}-proxy:1.0.0 proxy
docker build --tag {{ project-registry }}/{{ account }}-backend:1.0.0 backend
docker build --tag {{ project-registry }}/{{ account }}-stub:1.0.0 stub

docker swarm init
docker stack deploy --compose-file docker-compose.yml my-stack

docker stack ls
docker stack ps my-stack
docker stack services my-stack
docker ps -a

docker stack rm app-stack
```

docker config create
docker secret create

docker node ls
docker node ps

docker stack ls
docker stack ps
docker stack services

docker service ls
docker service ps

Изоляция данных
---------------
- [ ] Как контейнер работает с данными?
- [ ] Что происходит с изменениями в образе при остановке контейнера?
- [ ] Stateful VS Stateless containers
- [ ] Как зафиксировать изменения в образе?
- [ ] Как откатить изменения в образе?
- [ ] Как можно сохранять изменения на диске вне образа?
- Shared folders
- [Volumes](https://docs.docker.com/storage/volumes/)
- [ ] Жизненный цикл docker volume

Hands-on practice quest #05: multi-component _stateful_ application containerization
---------------------------

Виртуализация сети
------------------
- [ ] Отображение портов
- [ ] Варианты сетевой топологии между хостом и контейнером
- [ ] Варианты сетевой топологии между контейнерами
- [ ] Разрешение адресов и имен в виртуализированных сетях (+localhost issue)
docker network create | rm | prune
docker network connect | disconnect
docker network ls | inspect


Hands-on practice quest #06: _networked_ multi-component stateful application containerization
---------------------------

Изоляция хостовых ресурсов
--------------------------
- [ ] Отображение портов
- [ ] Установка переменных окружения ОС для контейнера
- [ ] Лимитирование памяти
- [ ] Лимитирование CPU
- [ ] Особенности настройки контейнеризированных приложений с учетом лимитирования ресурсов

Hands-on practice quest #07: networked multi-component stateful application _resource-limited_ containerization
---------------------------

Оптимизация сборки образов
--------------------------
- [ ] Директивы Dockerfile как слои образа
- [ ] Как уменьшить размер образа?
- [ ] Как ускорить сборку образа?
- [ ] Кеширование включаемых файлов и результатов директив
- [ ] Составные команды
- [ ] Порядок директив 
- [ ] Выбор образа-предка: легковесные ОС, busybox и `scratch`-образ
- [ ] Паттерн "multi-stage" для сборки и запуска

Hands-on practice quest #08: _build-optimized_ networked multi-component stateful application resource-limited containerization
---------------------------

Рекомендуемые практики
----------------------
- [ ] Запуск от выделенного пользователя
- [ ] Хранение и передача secrets
- [ ] Локальное журналирование и доступ к логам
- docker logging drivers
- shared folders or volumes
- sidecars containers
- remote log collectors
- [ ] Мониторинг
- [ ] "Docker-из-docker"?

Hands-on practice quest #09: build-optimized networked multi-component stateful application resource-limited _best practice based_ containerization
---------------------------

Docker в среде Kubernetes
-------------------------
- [ ] k8s больше не поддерживает docker: [все пропало](https://twitter.com/Dixie3Flatline/status/1334188913724850177)?
- [ ] Как устроен Docker? Вспоминаем элементы.
- [ ] Как устроены аналоги? [Элементы](https://www.threatstack.com/blog/diving-deeper-into-runtimes-kubernetes-cri-and-shims).

