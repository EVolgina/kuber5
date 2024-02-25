# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 2»
- Цель задания. В тестовой среде Kubernetes необходимо обеспечить доступ к двум приложениям снаружи кластера по разным путям.
## Задание 1. Создать Deployment приложений backend и frontend
- Создать Deployment приложения frontend из образа nginx с количеством реплик 3 шт. [fronend]()
- Создать Deployment приложения backend из образа multitool. [backend]()
- Добавить Service, которые обеспечат доступ к обоим приложениям внутри кластера. [svr-frontend](), [svr-backend]()
- Продемонстрировать, что приложения видят друг друга с помощью Service.
- Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.
```
vagrant@vagrant:~/kube/zad5$ kubectl apply -f back.yaml
deployment.apps/backend created
vagrant@vagrant:~/kube/zad5$ kubectl apply -f front.yaml
deployment.apps/frontend created
vagrant@vagrant:~/kube/zad5$ kubectl apply -f svr-back.yaml
service/svc-back created
vagrant@vagrant:~/kube/zad5$ kubectl apply -f svr-front.yaml
service/svc-front created
vagrant@vagrant:~/kube/zad5$ kubectl get deployments
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
multitool       1/1     1            1           6d23h
my-deployment   3/3     3            3           20h
backend         1/1     1            1           56s
frontend        3/3     3            3           44s
vagrant@vagrant:~/kube/zad5$ kubectl get services
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
kubernetes    ClusterIP   10.152.183.1     <none>        443/TCP             21d
my-service    ClusterIP   10.152.183.48    <none>        9001/TCP,9002/TCP   20h
my-service1   NodePort    10.152.183.118   <none>        80:30080/TCP        17h
svc-back      ClusterIP   10.152.183.107   <none>        80/TCP              44s
svc-front     ClusterIP   10.152.183.79    <none>        80/TCP              28s
```

