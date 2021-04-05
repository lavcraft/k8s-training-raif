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
- сформированы пары участников с чередованием ролей в паре 
- Hint: синонимы команд docker cli
- Hint: `... --help`
- [docker cli](https://docs.docker.com/engine/reference/commandline/docker/)


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
- [ ] Вспомогательные утилиты
- `docker run --rm -v /var/run/docker.sock:/var/run/docker.sock nate/dockviz images -t`
- `docker run -v /var/run/docker.sock:/run/docker.sock -ti -e TERM tomastomecek/sen`
- [dive](https://github.com/wagoodman/dive)

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
- Какие способы идентификации образа? 
- Какой тег у образа по умолчанию?
- В чем физический смысл удаления образа?
- Сколько места занимает образ на диске или в репозитории?

Жизненный цикл контейнера
-------------------------
- [ ] container = process + container data (container layer)
- [ ] Какие ресурсы хоста нужно выделить для запуска контейнера?
- [ ] Именование контейнера
- [ ] [Жизненный цикл контейнера](https://medium.com/@BeNitinAgarwal/lifecycle-of-docker-container-d2da9f85959)
- `docker container create` + `docker container start` = `docker container run`
- `docker container pause`, `docker container unpause`
- `docker container stop`
- `docker container rm`
- [ ] [Запуск контейнера в интерактивном и фоновом режимах](https://docs.docker.com/engine/reference/run/#detached--d): `-d` vs `-it`
- [ ] [Экстернализация](https://docs.docker.com/engine/reference/run/#env-environment-variables) конфигурации приложения при запуске контейнера
- [ ] [Форвардинг портов](https://docs.docker.com/engine/reference/commandline/run/#options)
- [ ] Просмотр активных контейнеров: работающих и остановленных
- [ ] Подключение к рабочему контейнеру
- [ ] Журнал контейнера
- [ ] Остановка контейнера
- [ ] Удаление контейнера и его физический смыл

Hands-on practice quest #02: container lifecycle
---------------------------
- [ ] Given пары участников

- [ ] When участники именуют сценарии, формируют свои команды и проверяют их вывод и поведение
- Сценарий "Как посмотреть список работающих и остановленных контейнеров?"
```shell
docker container ls --all
docker container ls --format '{{.ID}} | {{.Names}} | {{.Status}} | {{.Image}}' 
```

- Сценарий "Как запустить 'одноразовый' контейнер в интерактивном режиме?"
```shell
docker run --rm -it {{ images-registry }}/alpine
/# exit
docker container ls
```

- Сценарий "Как запустить контейнер с сервисом в фоновом режиме?"
```shell
docker container run --detach --name proxy1 --publish 80:80 nginx:1.19.4
docker container ls
```

- Сценарий "Как создать контейнер с сервисом без запуска?"
```shell
docker container create
docker container rename
```

- Сценарий "Как запустить созданный контейнер?"
```shell
docker container start
```

- Сценарий "Как поставить на паузу контейнер?"
```shell
docker container pause
docker container unpause
```

- Сценарий "Как остановить и запустить снова работающий контейнер?"
```shell
docker container stop
docker container start
docker container restart
```

- Сценарий "Как удалить работающий контейнер?"
```shell
docker container rm -f
docker container kill
docker container prune
```

- Сценарий "Как 'подключиться' к работающему контейнеру?"
```shell
docker container attach
docker container logs
docker container top
docker container exec /bin/sh
```

- Сценарий "Как обменяться файлами с контейнером?"
```shell
docker container cp
```

- Сценарий "Как посмотреть свойства контейнера?"
```shell
docker container port
docker container inspect
```

- Сценарий "Как узнать и сохранить container data (container layer)?"
```shell
docker container commit
docker container diff
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- Какие способы идентификации контейнера?
- Какое имя у контейнера по умолчанию?
- В чем физический смысл удаления контейнера?
- Сколько места занимает контейнер на диске?

Контейнеризация простого сервиса: образ "с нуля"
--------------------------------
- [ ] Что необходимо сконфигурировать для создания образа диска с простым сервисом?
- [ ] Экстернализация конфигурации приложения при сборке образа
- [ ] [Команда сборки образа](https://docs.docker.com/engine/reference/commandline/build/#tag-an-image--t) и роль Dockerfile
- [ ] Структура Dockerfile и его декларативность
- [ ] Ключевые [директивы Dockerfile](https://docs.docker.com/engine/reference/builder)
- `FROM`
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
- [ ] Given пары участников

- [ ] When участники именуют сценарии, формируют свои команды и проверяют их вывод и поведение
- Сценарий "Как описать provision образа в Dockerfile?"
```shell
cd application/backend
nano Dockerfile
```

- Сценарий "Как собрать свой образ с приложением на базе Dockerfile?"
```shell
docker image build --tag {{ project-registry }}/{{ account }}-backend:1.0.0 .
```

- Сценарий "Как запустить контейнер на базе своего образа с приложением?"
```shell
docker run --rm --detach --publish 8080:8080 --name backend --env SPRING_PROFILES_ACTIVE=qa --volume $(pwd)/log:/dbo/log {{ project-registry }}/{{ account }}-backend:1.0.0
```

- Сценарий "Как сохранить образ в репозитории?"
```shell
docker login
docker push
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- В каком порядке выполнялись директивы Dockerfile?


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
- [docker network](#Виртуализация сети)
- [docker configs](https://docs.docker.com/engine/swarm/configs/)
- [docker secrets](https://docs.docker.com/engine/swarm/secrets/)

Hands-on practice quest #04: _multi-component_ application containerization
---------------------------
- [ ] Given пары участников

- [ ] When участники именуют сценарии, формируют свои команды и проверяют их вывод и поведение
- Сценарий "Как ...?"
```shell
cd application
nano proxy/nginx.conf #TODOs

docker build --tag {{ project-registry }}/{{ account }}-proxy:1.0.0 proxy
docker build --tag {{ project-registry }}/{{ account }}-backend:1.0.0 backend
docker build --tag {{ project-registry }}/{{ account }}-stub:1.0.0 stub
```

- Сценарий "Как ...?"
```shell
cd application
cat docker-compose.yml

docker swarm init
docker stack deploy --compose-file docker-compose.yml my-stack
```

- Сценарий "Как ...?"
```shell
docker stack ls
docker stack ps my-stack
docker stack services my-stack
docker ps -a
```

- Сценарий "Как ...?"
```shell
docker stack rm app-stack
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- Какой оркестратор использовался?
- Какие ресурсы были виртуализированы?

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
- [ ] Given пары участников

- [ ] When участники именуют сценарии, формируют свои команды и проверяют их вывод и поведение
- Сценарий "Как сохранить измененное состояние контейнера в качестве отдельного идентифицируемого образа?"
```shell
docker run -it alpine /bin/sh
/# vi side-effect
/# exit

docker container commit
docker image ls
```

- Сценарий "Как пробросить shared folder с хостовой системы в контейнер?"
```shell
docker run -v
```

- Сценарий "Как подключить volume в контейнер?"
```shell
docker run -v
```

- Сценарий "Как посмотреть текущие volume?"
```shell
docker volume ls [--all]
```

- Сценарий "Как удалить неиспользуемые volume?"
```shell
docker volume 
```

- Сценарий "Как управлять volume и shared folder в docker-compose?"
```shell
cd application
nano docker-compose.yml
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- Где физически храняться volume?
- Что такое "неиспользуемые" volume?

Виртуализация сети
------------------
- [x] Отображение портов
- [ ] Варианты сетевой топологии между хостом и контейнером
- [ ] Варианты сетевой топологии между контейнерами
- [ ] Разрешение адресов и имен в виртуальных сетях (+localhost issue)

Hands-on practice quest #06: _networked_ multi-component stateful application containerization
---------------------------
- [ ] Given пары участников

- [ ] When участники именуют сценарии, формируют свои команды и проверяют их вывод и поведение
- Сценарий "Как управлять виртуальными сетями?"
```shell
docker network create
docker network rm
docker network prune
```

- Сценарий "Как подключить контейнер к виртуальным сетям?"
```shell
docker network connect 
docker network disconnect
```

- Сценарий "Как посмотреть состояние виртуальных сетей?"
```shell
docker network ls 
docker network inspect
```

- Сценарий "Как управлять network в docker-compose?"
```shell
cd application
nano docker-compose.yml
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- Какая сетвая топология определена в `docker-compose`? 

Изоляция хостовых ресурсов
--------------------------
- [x] Отображение портов
- [x] Установка переменных окружения ОС для контейнера
- [ ] [Лимитирование памяти и CPU](https://docs.docker.com/config/containers/resource_constraints/) 
- [ ] [Просмотр статистики](https://docs.docker.com/engine/reference/commandline/stats/)
- [ ] Лимитирование ресурсов при [запуске контейнера](https://docs.docker.com/engine/reference/run/#runtime-constraints-on-resources)
- [ ] Лимитирование ресурсов в [docker-compose](https://docs.docker.com/compose/compose-file/compose-file-v3/#deploy)
- [ ] [Как ведет себя одиночный контейнер](https://docs.docker.com/engine/reference/run/#restart-policies---restart) при ошибках контейнеризуемого приложения
- [ ] [Как ведет себя контейнер в swarm](https://docs.docker.com/compose/compose-file/compose-file-v3/#deploy) при ошибках контейнеризуемого приложения
- [ ] Особенности настройки контейнеризированных приложений с учетом лимитирования ресурсов

Hands-on practice quest #07: networked multi-component stateful application _resource-limited_ containerization
---------------------------
- [ ] Given пары участников

- [ ] When участники именуют сценарии, формируют свои команды и проверяют их вывод и поведение
- Сценарий "Как понять текущее потребление ресурсов?"
```shell
docker stats
```

- Сценарий "Как лимитировать ресурсы при запуске контейнера?"
```shell
docker run # ограничить по CPU и памяти, чтобы получить OOME
```

- Сценарий "Как лимитировать ресурсы в docker-compose?"
```shell
cd application
nano docker-compose.yml # ограничить по CPU и памяти, чтобы получить OOME
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- Какие лимиты на ресурсы устанавливаются Docker по умолчанию?
- Какое поведение отдельного контейнера при OOME по умолчанию?
- Какое поведение контейнера в Swarm при OOME по умолчанию?

Оптимизация сборки образов
--------------------------
- [ ] Директивы Dockerfile как слои образа
- [ ] Как уменьшить размер образа?
- [ ] Как ускорить сборку образа?
- [ ] Кеширование включаемых файлов и результатов директив
- [ ] Составные команды
- [ ] Порядок директив 
- [ ] Выбор образа-предка: легковесные ОС, busybox и `scratch`-образ
- [ ] Паттерн "multi-stage build" для сборки и запуска

Hands-on practice quest #08: _build-optimized_ networked multi-component stateful application resource-limited containerization
---------------------------
- [ ] Given пары участников

- [ ] When участники именуют сценарии, формируют свои команды и проверяют их вывод и поведение
- Сценарий "Как оптимизировать сборку?"
```shell
nano application/backend/Dockerfile # TODO оптимизировать сборку и убедиться в ускорении
nano application/proxy/Dockerfile
nano application/stub/Dockerfile
```

- Сценарий "Как управлять кешем сборки?"
```shell
docker builder prune
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?
- Насколько получилось оптимизировать сборки?

Рекомендуемые практики
----------------------
- [ ] Минимизировать [security риски](https://snyk.io/blog/10-docker-image-security-best-practices)
- Используйте минимальные образы: быстрее и меньше зависимостей – меньше рисков 
- Запуск от выделенного пользователя с минимальными привилегиями с возможностью при запуске [добавить прав](https://docs.docker.com/engine/reference/run/#additional-groups)
- Подписывать образы и проверять подписи
- Сканеры уязвимостей для образов
- Линтеры для Dockerfile
- multi-stage builds, чтобы в образ не утекли чувствительные данные
- аккуратно с рекурсивным копированием + .dockerignore
- COPY вместо ADD
- фиксированные теги для идентификации образов
- [ ] Хранение и передача чувствительных данных как [Docker secrets](https://www.docker.com/blog/docker-secrets-management/)
- [ ] Локальное журналирование и доступ к логам
- docker [logging drivers](https://docs.docker.com/config/containers/logging/configure/)
- dedicated logs shared folders/volumes
- remote log collectors
- [ ] Мониторинг
- [Docker как Prometheus target](https://docs.docker.com/config/daemon/prometheus/)
- [ ] "Docker-из-docker"?

Hands-on practice quest #09: build-optimized networked multi-component stateful application resource-limited _best practice based_ containerization
---------------------------
- [ ] Given пары участников

- [ ] When участники именуют сценарии, формируют свои команды и проверяют их вывод и поведение
- Сценарий "Как ...?"
```shell
docker info --format '{{.LoggingDriver}}'
docker docker run -it --log-driver local --log-opt mode=non-blocking --log-opt max-buffer-size=4m 
docker inspect -f '{{.HostConfig.LogConfig.Type}}'
docker logs 
```
- Сценарий "Как ...?"
```shell
nano /etc/docker/daemon.json
curl 127.0.0.1:9323/metrics
```

- [ ] Then участники делятся проблемами и отвечают на вопросы
- Как проименовали сценарии?

Docker в среде Kubernetes
-------------------------
- [ ] k8s больше не поддерживает docker: [все пропало](https://twitter.com/Dixie3Flatline/status/1334188913724850177)?
- [ ] Как устроен Docker? Вспоминаем элементы.
- [ ] Как устроены аналоги? [Элементы](https://www.threatstack.com/blog/diving-deeper-into-runtimes-kubernetes-cri-and-shims).

