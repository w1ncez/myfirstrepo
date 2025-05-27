# Домашнее задание к занятию «Kubernetes. Часть 2» - Винцентини Сергей

### Задание 1

**Выполните действия:**

1. Создайте свой кластер с помощью kubeadm.
2. Установите любой понравившийся CNI плагин.
3. Добейтесь стабильной работы кластера.

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubeadm join <MASTER_IP>:<PORT> --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
kubectl get nodes
```



### Задание 2

Есть файл с деплоем:

```yaml
---
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

1. Создайте Helm Charts.
2. Добавьте в него сервис.
3. Вынесите все нужные, на ваш взгляд, параметры в `values.yaml`.
4. Запустите чарт в своём кластере и добейтесь его стабильной работы.

Создаем Helm Chart
```bash
helm create my-redis-chart
cd my-redis-chart
```

Вносим изменения в файл values.yaml
```yaml
replicaCount: 1
image:
  repository: bitnami/redis
  tag: latest
env:
  REDIS_PASSWORD: password123
service:
  type: ClusterIP
  port: 6379
```

Создаем шаблон Service в templates/service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "my-redis-chart.fullname" . }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - protocol: TCP
      port: {{ .Values.service.port }}
      targetPort: 6379
  selector:
    app: {{ include "my-redis-chart.name" . }}
```

Собираем и развертываем свой chart

```bash
helm package .
helm install my-redis ./
```

Проверяем статус развертывания:
```bash
kubectl get pods
```

