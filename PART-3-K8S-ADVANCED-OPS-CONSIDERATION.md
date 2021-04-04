Advanced Kubernetes — Operational Consideration
===============================================

Agenda
======

RBAC Introduction
-----------------

- [ ] Введение в модель Subject/API-Resources/Operations
- [ ] Kubernetes Roles and RoleBinding
- [ ] Access and Quota management. Cpu, memory, storage, storageclass
- [ ] Объекты ResourceQuota и soft/hard ограничения
- [ ] Минимальные и максимальные ограничения. Объекты LimitRange


Hands-on practice quest #00: Working with roles
-----------------------------------------------
- [ ] Given пары участников имеют задеплоенную версию приложений и сервисов и ingress
- [ ] When участники запускают команды и применяют новую настройки

Конфигурируем для одного приложение rediness/liveness для другого нет

```shell
kubectl api-resources
```

- [ ] Then участники делятся результатами и соображениями
- Как вы думаете, почему нет NamespaceRole а только ClusterRole/ClusterRoleBinding ?

