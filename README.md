# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 2»
- Цель задания. В тестовой среде Kubernetes необходимо обеспечить доступ к двум приложениям снаружи кластера по разным путям.
## Задание 1. Создать Deployment приложений backend и frontend
- Создать Deployment приложения frontend из образа nginx с количеством реплик 3 шт. [fronend](https://github.com/EVolgina/kuber5/blob/main/front.yaml)
- Создать Deployment приложения backend из образа multitool. [backend](https://github.com/EVolgina/kuber5/blob/main/back.yaml)
- Добавить Service, которые обеспечат доступ к обоим приложениям внутри кластера. [svr-frontend](https://github.com/EVolgina/kuber5/blob/main/svr-front.yaml), [svr-backend](https://github.com/EVolgina/kuber5/blob/main/svr-back.yaml)
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
- Создать Ingress, обеспечивающий доступ снаружи по IP-адресу кластера MicroK8S так, чтобы при запросе только по адресу открывался frontend а при добавлении /api - backend.[ingress](https://github.com/EVolgina/kuber5/blob/main/ingress.yaml)
- Продемонстрировать доступ с помощью браузера или curl с локального компьютера.
- Предоставить манифесты и скриншоты или вывод команды п.2.
```
vagrant@vagrant:~/kube/zad5$ microk8s enable ingress
Infer repository core for addon ingress
Enabling Ingress
ingressclass.networking.k8s.io/public created
ingressclass.networking.k8s.io/nginx created
namespace/ingress created
serviceaccount/nginx-ingress-microk8s-serviceaccount created
clusterrole.rbac.authorization.k8s.io/nginx-ingress-microk8s-clusterrole created
role.rbac.authorization.k8s.io/nginx-ingress-microk8s-role created
clusterrolebinding.rbac.authorization.k8s.io/nginx-ingress-microk8s created
rolebinding.rbac.authorization.k8s.io/nginx-ingress-microk8s created
configmap/nginx-load-balancer-microk8s-conf created
configmap/nginx-ingress-tcp-microk8s-conf created
configmap/nginx-ingress-udp-microk8s-conf created
daemonset.apps/nginx-ingress-microk8s-controller created
Ingress is enabled
vagrant@vagrant:~/kube/zad5$ vagrant@vagrant:~/kube/zad5$ kubectl apply -f ingress.yaml
ingress.networking.k8s.io/ingress created
vagrant@vagrant:~/kube/zad5$ kubectl describe ingress
Name:             ingress
Labels:           <none>
Namespace:        default
Address:
Ingress Class:    <none>
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /      svc-front:80 (10.1.52.147:80,10.1.52.163:80,10.1.52.189:80)
              /api   svc-back:80 (10.1.52.174:80)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:       <none>
vagrant@vagrant:~/kube/zad5$ kubectl get pod -o wide
NAME                           READY   STATUS    RESTARTS       AGE    IP            NODE      NOMINATED NODE   READINESS GATES
backend-7bfd89b7c7-pbqp6       1/1     Running   1 (61m ago)    129m   10.1.52.176   vagrant   <none>           <none>
frontend-7766f6c5f8-mkczf      1/1     Running   1 (61m ago)    128m   10.1.52.160   vagrant   <none>           <none>
frontend-7766f6c5f8-8t8dv      1/1     Running   1 (61m ago)    128m   10.1.52.164   vagrant   <none>           <none>
frontend-7766f6c5f8-xr82f      1/1     Running   1 (61m ago)    128m   10.1.52.169   vagrant   <none>           <none>
multitool-7f8c7df657-h2m9s     1/1     Running   6 (61m ago)    23h    10.1.52.145   vagrant   <none>           <none>
multitool-pod                  1/1     Running   22 (61m ago)   20h    10.1.52.178   vagrant   <none>           <none>
my-deployment-c57565bf-nc9n2   2/2     Running   10 (61m ago)   19h    10.1.52.173   vagrant   <none>           <none>
my-deployment-c57565bf-zk65h   2/2     Running   10 (61m ago)   19h    10.1.52.141   vagrant   <none>           <none>
my-deployment-c57565bf-8ffm8   2/2     Running   10 (61m ago)   19h    10.1.52.132   vagrant   <none>           <none>
vagrant@vagrant:~/kube/zad5$ kubectl get deployment -o wide
NAME            READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS                            IMAGES                                        SELECTOR
backend         1/1     1            1           129m   multitool                             wbitt/network-multitool                       app=backend
frontend        3/3     3            3           129m   nginx                                 nginx                                         app=frontend
multitool       1/1     1            1           7d1h   network-multitool                     wbitt/network-multitool                       app=multitool
my-deployment   3/3     3            3           22h    nginx-container,multitool-container   nginx:latest,wbitt/network-multitool:latest   app=my-app
vagrant@vagrant:~/kube/zad5$ kubectl get service -o wide
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE    SELECTOR
kubernetes    ClusterIP   10.152.183.1     <none>        443/TCP             21d    <none>
my-service    ClusterIP   10.152.183.48    <none>        9001/TCP,9002/TCP   22h    app=my-app
my-service1   NodePort    10.152.183.118   <none>        80:30080/TCP        19h    app=my-app
svc-back      ClusterIP   10.152.183.107   <none>        80/TCP              129m   app=backend
svc-front     ClusterIP   10.152.183.79    <none>        80/TCP              129m   app=frontend
vagrant@vagrant:~/kube/zad5$ curl http://10.152.183.107:80
WBITT Network MultiTool (with NGINX) - backend-7bfd89b7c7-pbqp6 - 10.1.52.176 - HTTP: 80 , HTTPS: 443 . (Formerly praqma/network-multitool)
vagrant@vagrant:~/kube/zad5$ curl http://10.152.183.79
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
```
