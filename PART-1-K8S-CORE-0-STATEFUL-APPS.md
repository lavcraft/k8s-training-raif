K8S Statefull Apps
==================
4 часа

Введение
--------------

- [ ] PV and PVC
- [ ] Static vs Dynamic Provisioning
- [ ] StatefulSet

Hands-on practice quest #00: working with pvc and statefulset
-------------------------------------------------------------

- [ ] Given сформированы пары участников
- [ ] When участники используют команды и проанализируют их вывод и поведение

```shell script
kubectl explain pvc
kubectl get storageclasses

cat >sandwich-ingridients.yml <<EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: sandwich-ingridients
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Mi
EOF
```

- [] Задание: монтируйте директорию в какой либо Pod. Запишите данные и попробуйте его пересоздать

```shell
kubectl explain pod.spec.volumes.persistentVolumeClaim
kubectl explain pod.spec.containers.volumeMounts

vi sandwich.yml
kubectl apply -f sandwich.yml
kubectl exec -it <mypod> -- sh
echo "tasty potatos" > <mountdir>/potato

kubectl delete pod <mypod>
kubectl apply -f sandwich.yml
```

- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- [ ] На каком узле запустился под после пересоздания пода?
- [ ] Можно ли перенести под на другую ноду?
- [ ] Можно ли запустить ещё один под с этим pvc? Попробуйте
- [ ] Удаляется ли PVC после удаления Pod?
- [ ] Можно ли удалить PVC до удаления Pod?

- [] Задание: создайте statefulset
- Материалы - [Creating a StatefulSet](https://kubernetes.io/docs/tutorials/stateful-application/basic-stateful-set/#creating-a-statefulset)


```shell
# переделываем на StatefulSet
vi sandwich.yml
kubectl apply -f sandwich.yml
kubectl get pods
kubectl get pvc
kubectl exec -it debug -- sh
[debug]$ nslookup <pod-static-name>.<stateful-service-name>
```

Задание:
- [ ] зайдите в контейнер и запишите данные. 
- [ ] Удалите контейнер и наблюдайте за состоянием statefulset. 
- [ ] Проверьте что стало с данными

Подведите итоги:
- [ ] Then участники делятся возникшими и решенными проблемами и отвечают на вопросы
- Как возникли сложности?
- Для чего нужен `serviceName` в StatefulSet?
- Может ли у пода из StatefulSet меняться IP?
- Для чего стоит использовать Service с `clusterIP: None`?
- Когда удаляются PVC?
- Когда стоит использовать статический PV?
