Advanced Kubernetes — Monitoring
================================
4 часа

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
- Какие версии kubectl совместимы с какими версиями кластера?

K8S Authentication
------------------
- [ ] Как управлять кластерами? CLI or GUI ?
- [ ] kubectl CLI и много кластеров. Как понять где себя обслужить
- [ ] kubectl contexts



