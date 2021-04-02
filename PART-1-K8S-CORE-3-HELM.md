Kubernetes Charts
=================
4 часов, 1 ак. дня.

Prerequisites
=============

- [ ] RAM ≥ 8Gb
- [ ] [Пройденыне задание с тренига по Docker](#link-to-docker-part)
- [ ] [Пройденыне задание с тренига по K8S-CORE](#link-to-docker-part)
- [ ] [Развёрнутый кластер k8s на пару](#link-to-confluence)
- [ ] terminal
- [ ] kubectl cli


K8S Helm chars introduction
---------------------------

- [ ] Для чего придуман, предпосылки
- [ ] Связь с multi service deploy и управление группой ресурсов как одной сущностью
- [ ] Сторонние Helm и их поддержка/обновление

Hands-on practice quest #00: создание Helm
------------------------------------------

```shell
helm create
ls -la
```

```shell
vi Chart.yaml
vi values.yaml 
```

```shell
ls -la templates
vi templates/deploymeny.yaml
vi templates/service.yaml
vi templates/ingress.yaml 
```

```shell
helm install
```


- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы

- что будет если несколько раз запустить helm install? Безопасное ли это поведение?
- как версионировать helm charts?


K8S Helm chars
--------------

- [ ] .tpl шаблоны и go template language.
- [ ] команды helm template
- [ ] определение переменных для деплоя нашего приложения с помощью helm

Hands-on practice quest #01: шаблонизирование
------------------------------------------

```shell
helm template <> . -s templates/deployment.taml
```

```shell
vi values.yaml
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы

- как сделать дефолтные значения для переменных шаблона?
- как откатить helm?
- что будет с stateful приложениями при откате?
