# Домашнее задание к занятию «Kubernetes. Часть 1»

### Задание 1

**Выполните действия:**

1. Запустите Kubernetes локально, используя k3s или minikube на свой выбор.
2. Добейтесь стабильной работы всех системных контейнеров.

```
w1nce@ubuntu:~$ kubectl get po -A

NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
kube-system   coredns-674b8bbfcf-xhcm2           1/1     Running   0             11m
kube-system   etcd-minikube                      1/1     Running   0             11m
kube-system   kube-apiserver-minikube            1/1     Running   0             11m
kube-system   kube-controller-manager-minikube   1/1     Running   0             11m
kube-system   kube-proxy-h22df                   1/1     Running   0             11m
kube-system   kube-scheduler-minikube            1/1     Running   0             11m
kube-system   storage-provisioner                1/1     Running   1 (10m ago)   11m

```

---

### Задание 2

Есть файл с деплоем:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: master
        image: bitnami/redis
        env:
         - name: REDIS_PASSWORD
           value: password123
        ports:
        - containerPort: 6379
```


**Выполните действия:**

1. Измените файл с учётом условий:

 * redis должен запускаться без пароля;
 * создайте Service, который будет направлять трафик на этот Deployment;
 * версия образа redis должна быть зафиксирована на 6.0.13.

Итоговый файл деплоя:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: master
        image: bitnami/redis:6.0.13
        ports:
        - containerPort: 6379
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
---
apiVersion: v1
kind: Service
metadata:
  name: redis
spec:
  selector:
    app: redis
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379
  type: ClusterIP
```

2. Запустите Deployment в своём кластере и добейтесь его стабильной работы.

```
w1nce@ubuntu:~/k8s$ kubectl apply -f redis.yaml
deployment.apps/redis created
service/redis created


w1nce@ubuntu:~/k8s$ kubectl get deployments
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
redis   0/1     1            0           10s


w1nce@ubuntu:~/k8s$ kubectl get pods
NAME                    READY   STATUS             RESTARTS     AGE
redis-b7c5b9664-bsjcx   0/1     CrashLoopBackOff   1 (9s ago)   18s


w1nce@ubuntu:~/k8s$ kubectl get service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP    24m
redis        ClusterIP   10.97.219.195   <none>        6379/TCP   26s

```



### Задание 3

**Выполните действия:**

1. Напишите команды kubectl для контейнера из предыдущего задания:

 - выполнения команды ps aux внутри контейнера;
 - просмотра логов контейнера за последние 5 минут;
 - удаления контейнера;
 - проброса порта локальной машины в контейнер для отладки.

```
w1nce@ubuntu:~/k8s$ kubectl exec -it redis-54457d549d-7dh8r -- ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
1001           1  0.2  0.0  52820  7496 ?        Ssl  06:19   0:00 redis-server 0.0.0.0:6379
1001          38  0.0  0.0   7644  2588 pts/0    Rs+  06:20   0:00 ps aux


w1nce@ubuntu:~/k8s$ kubectl logs redis-54457d549d-7dh8r --since=5m
redis 06:19:54.26
redis 06:19:54.26 Welcome to the Bitnami redis container
redis 06:19:54.26 Subscribe to project updates by watching https://github.com/bitnami/bitnami-docker-redis
redis 06:19:54.27 Submit issues and feature requests at https://github.com/bitnami/bitnami-docker-redis/issues
redis 06:19:54.27
redis 06:19:54.27 INFO  ==> ** Starting Redis setup **
redis 06:19:54.28 WARN  ==> You set the environment variable ALLOW_EMPTY_PASSWORD=yes. For safety reasons, do not use this flag in a production environment.
redis 06:19:54.28 INFO  ==> Initializing Redis
redis 06:19:54.30 INFO  ==> Setting Redis config file
redis 06:19:54.32 INFO  ==> ** Redis setup finished! **

redis 06:19:54.33 INFO  ==> ** Starting Redis **
1:C 26 May 2025 06:19:54.345 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 26 May 2025 06:19:54.345 # Redis version=6.0.13, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 26 May 2025 06:19:54.345 # Configuration loaded
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 6.0.13 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 1
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

1:M 26 May 2025 06:19:54.352 # Server initialized
1:M 26 May 2025 06:19:54.353 * Ready to accept connections


w1nce@ubuntu:~/k8s$ kubectl delete pod redis-54457d549d-7dh8r
pod "redis-54457d549d-7dh8r" deleted
w1nce@ubuntu:~/k8s$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
redis-54457d549d-t4qm5   1/1     Running   0          41s


w1nce@ubuntu:~/k8s$ kubectl port-forward redis-54457d549d-t4qm5 6379:6379
Forwarding from 127.0.0.1:6379 -> 6379
Forwarding from [::1]:6379 -> 6379




```

---

### Задание 4

Есть конфигурация nginx:

```
location / {
    add_header Content-Type text/plain;
    return 200 'Hello from k8s';
}
```

**Выполните действия:**

Напишите yaml-файлы для развёртки nginx, в которых будут присутствовать:

 - ConfigMap с конфигом nginx;
 - Deployment, который бы подключал этот configmap;
 - Ingress, который будет направлять запросы по префиксу /test на наш сервис.


Файл nginx-configmap.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  nginx.conf: |
    events {}
    http {
      server {
        listen 80;
        location / {
          add_header Content-Type text/plain;
          return 200 'Hello from k8s';
        }
      }
    }
```

Файл nginx-deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - name: nginx-config-volume
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf
      volumes:
      - name: nginx-config-volume
        configMap:
          name: nginx-config
```

Файл nginx-ingress.yaml
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: nginx.local
    http:
      paths:
      - path: /test
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80
```

Файл nginx-service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

Развертывание
```
w1nce@ubuntu:~/k8s/nginx$ kubectl apply -f nginx-configmap.yaml
configmap/nginx-config created

w1nce@ubuntu:~/k8s/nginx$ kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx created

w1nce@ubuntu:~/k8s/nginx$ kubectl apply -f nginx-service.yaml
service/nginx created

w1nce@ubuntu:~/k8s/nginx$ kubectl apply -f nginx-ingress.yaml
ingress.networking.k8s.io/nginx-ingress unchanged

w1nce@ubuntu:~/k8s/nginx$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-c946b7f67-jqbzm    1/1     Running   0          19s
redis-54457d549d-t4qm5   1/1     Running   0          14m

w1nce@ubuntu:~/k8s/nginx$ kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP    51m
nginx        ClusterIP   10.101.31.30    <none>        80/TCP     21s
redis        ClusterIP   10.97.219.195   <none>        6379/TCP   27m

w1nce@ubuntu:~/k8s/nginx$ kubectl get ingress
NAME            CLASS    HOSTS         ADDRESS   PORTS   AGE
nginx-ingress   <none>   nginx.local             80      63s

```
