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
- [dockercli](https://docs.docker.com/engine/reference/commandline/cli/)
- [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)
- [containerd](https://www.docker.com/blog/what-is-containerd-runtime/)
- [runc](https://www.threatstack.com/blog/diving-deeper-into-runtimes-kubernetes-cri-and-shims)
- контейнеризованное приложение
- disk image provisioning tool (dockercli) and language: Dockerfile
- disk image
- Docker image registries: [docker hub](http://hub.docker.com) and corporate registries
- container
- [ ] Registry authentication and credentials storing

Hands-on practice quest #00: prerequisites sound-check
---------------------------
- [ ] Given 
- сформированы пары участников
- проставлены закладки на основные ресурсы раздела [Prerequisites](#Prerequisites)

- [ ] When участники используют команды и проанализируют их вывод и поведение
```shell
docker --version
```
```shell
docker login {{ registry-host }}
docker pull {{ images-registry }}/postgres:11-alpine
```
```shell
docker run -it {{ images-registry }}/postgres:11-alpine /bin/sh
$> exit
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Успешна ли сконфигурирована система для использования docker?
- Откуда взялся образ диска?
- Какой раз по счету был запущен на данной машине контейнер с этим образом?

Жизненный цикл готового образа
------------------------------
- [ ] Задача среды исполнения контейнеров: изоляция диска
- [ ] Отображение диска контейнера на диск хостовой системы: образ
- [ ] Хранение образа на хостовой системе и в репозиториях
- [ ] Что должно быть на диске для запуска и работы контейнеризованного приложения?
- [ ] Состав образа диска (от scratch до prod-ready)
- [ ] Жизненный цикл образа и аналогии с git
- [ ] Прием copy-on-write и OverlayFS
- [ ] Идентификация образов: имена, версии, теги
- [ ] Semantic versioning vs unique tags

Hands-on practice quest #01: pre-built disk image lifecycle
---------------------------
- [ ] Given пары участников
- [ ] When участники используют команды и проанализируют их вывод и поведение
```shell
docker images
docker images --all
```
```shell
docker pull {{ images-registry }}/postgres:11-alpine
docker images --all
```
```shell
docker rmi {{ images-registry }}/postgres:11-alpine [--force]
docker images
```
- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Сколько контейнеров было на хосте?
- Сколько места на диске хоста занимает каждый образ?
- Сколько места на диске хоста занимает каждый контейнер?
- Какие приложения были контейнеризованы?

Жизненный цикл контейнера
-------------------------
- [ ] Какие ресурсы хоста нужно выделить для запуска контейнера?
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

Контейнеризация простого сервиса: образ "с нуля"
--------------------------------
- [ ] Что необходимо сконфигурировать для создания образа диска с простым сервисом?
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
```shell
cd /docker-application/application/backend
docker build --tag {{ project-registry }}/{{ account }}-backend:1.0.0 .
docker run --rm --detach --publish 8080:8080 --name backend --env SPRING_PROFILES_ACTIVE=qa --volume $(pwd)/log:/dbo/log {{ project-registry }}/{{ account }}-backend:1.0.0
```

Введение в контейнеризацию составного приложения
------------------------------------------------
- [ ] Обзор Docker Compose и Docker Stack
- [ ] C Dockerfile и без него
- [ ] Структура и пример docker-compose.yml
- [ ] Ограничения Docker Compose и Docker Stack

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

