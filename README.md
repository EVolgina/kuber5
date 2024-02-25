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
vagrant@vagrant:~/kube/zad5$ kubectl get pods
NAME                           READY   STATUS    RESTARTS         AGE
multitool-7f8c7df657-h2m9s     1/1     Running   5 (68m ago)      21h
my-deployment-c57565bf-nc9n2   2/2     Running   8 (68m ago)      17h
my-deployment-c57565bf-8ffm8   2/2     Running   8 (68m ago)      17h
my-deployment-c57565bf-zk65h   2/2     Running   8 (68m ago)      17h
backend-7bfd89b7c7-pbqp6       1/1     Running   0                4m33s
frontend-7766f6c5f8-mkczf      1/1     Running   0                4m20s
frontend-7766f6c5f8-8t8dv      1/1     Running   0                4m20s
frontend-7766f6c5f8-xr82f      1/1     Running   0                4m20s
multitool-pod                  1/1     Running   20 (2m31s ago)   18h
```
- Приложения видят друг друга
```
vagrant@vagrant:~/kube/zad5$ kubectl exec -it frontend-7766f6c5f8-mkczf -- sh
# curl svc-back:80
WBITT Network MultiTool (with NGINX) - backend-7bfd89b7c7-pbqp6 - 10.1.52.174 - HTTP: 80 , HTTPS: 443 . (Formerly praqma/network-multitool)
#
```
```
vagrant@vagrant:~/kube/zad5$ kubectl exec -it backend-7bfd89b7c7-pbqp6 -- sh
/ # curl svc-front:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
/ #
```
## Задание 2. Создать Ingress и обеспечить доступ к приложениям снаружи кластера
- Включить Ingress-controller в MicroK8S.
- Создать Ingress, обеспечивающий доступ снаружи по IP-адресу кластера MicroK8S так, чтобы при запросе только по адресу открывался frontend а при добавлении /api - backend.
- Продемонстрировать доступ с помощью браузера или curl с локального компьютера.
- Предоставить манифесты и скриншоты или вывод команды п.2.
```

```
