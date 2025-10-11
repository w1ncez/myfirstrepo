# Домашнее задание к занятию  «Очереди RabbitMQ» - Винцентини С.Г.

---

### Задание 1. Установка RabbitMQ

Используя Vagrant или VirtualBox, создайте виртуальную машину и установите RabbitMQ.
Добавьте management plug-in и зайдите в веб-интерфейс.

*Итогом выполнения домашнего задания будет приложенный скриншот веб-интерфейса RabbitMQ.*

![Web RMQ GUI](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/RMQ/rmq1.jpg?raw=true)
---

### Задание 2. Отправка и получение сообщений

Используя приложенные скрипты, проведите тестовую отправку и получение сообщения.
Для отправки сообщений необходимо запустить скрипт producer.py.

Для работы скриптов вам необходимо установить Python версии 3 и библиотеку Pika.
Также в скриптах нужно указать IP-адрес машины, на которой запущен RabbitMQ, заменив localhost на нужный IP.

```shell script
$ pip install pika
```

Зайдите в веб-интерфейс, найдите очередь под названием hello и сделайте скриншот.
После чего запустите второй скрипт consumer.py и сделайте скриншот результата выполнения скрипта

*В качестве решения домашнего задания приложите оба скриншота, сделанных на этапе выполнения.*

Для закрепления материала можете попробовать модифицировать скрипты, чтобы поменять название очереди и отправляемое сообщение.

![hello](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/RMQ/rmq2.jpg?raw=true)
![hello2](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/RMQ/rmq3.jpg?raw=true)

---

### Задание 3. Подготовка HA кластера

Используя Vagrant или VirtualBox, создайте вторую виртуальную машину и установите RabbitMQ.
Добавьте в файл hosts название и IP-адрес каждой машины, чтобы машины могли видеть друг друга по имени.

Пример содержимого hosts файла:
```shell script
$ cat /etc/hosts
192.168.0.10 rmq01
192.168.0.11 rmq02
```
После этого ваши машины могут пинговаться по имени.

Затем объедините две машины в кластер и создайте политику ha-all на все очереди.

*В качестве решения домашнего задания приложите скриншоты из веб-интерфейса с информацией о доступных нодах в кластере и включённой политикой.*

Также приложите вывод команды с двух нод:

![ha-cluster](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/RMQ/rmq5.jpg?raw=true)

```shell script
$ rabbitmqctl cluster_status
```
```
root@ubuntu:/home/w1nce# rabbitmqctl cluster_status
Cluster status of node rabbit@ubuntu ...
Basics

Cluster name: rabbit@ubuntu
Total CPU cores available cluster-wide: 4

Cluster Tags

(none)

Disk Nodes

rabbit@ubuntu
rabbit@ubuntu1

Running Nodes

rabbit@ubuntu
rabbit@ubuntu1

Versions

rabbit@ubuntu: RabbitMQ 4.1.4 on Erlang 27.3.4.2
rabbit@ubuntu1: RabbitMQ 4.1.4 on Erlang 27.3.4.2

CPU Cores

Node: rabbit@ubuntu, available CPU cores: 2
Node: rabbit@ubuntu1, available CPU cores: 2

Maintenance status

Node: rabbit@ubuntu, status: not under maintenance
Node: rabbit@ubuntu1, status: not under maintenance

Alarms

(none)

Network Partitions

(none)

Listeners

Node: rabbit@ubuntu, interface: [::], port: 15672, protocol: http, purpose: HTTP API
Node: rabbit@ubuntu, interface: [::], port: 25672, protocol: clustering, purpose: inter-node and CLI tool communication
Node: rabbit@ubuntu, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0-9-1 and AMQP 1.0
Node: rabbit@ubuntu1, interface: [::], port: 15672, protocol: http, purpose: HTTP API
Node: rabbit@ubuntu1, interface: [::], port: 25672, protocol: clustering, purpose: inter-node and CLI tool communication
Node: rabbit@ubuntu1, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0-9-1 and AMQP 1.0

Feature flags

Flag: classic_mirrored_queue_version, state: enabled
Flag: classic_queue_type_delivery_support, state: enabled
Flag: detailed_queues_endpoint, state: enabled
Flag: direct_exchange_routing_v2, state: enabled
Flag: drop_unroutable_metric, state: enabled
Flag: empty_basic_get_metric, state: enabled
Flag: feature_flags_v2, state: enabled
Flag: implicit_default_bindings, state: enabled
Flag: khepri_db, state: disabled
Flag: listener_records_in_ets, state: enabled
Flag: maintenance_mode_status, state: enabled
Flag: message_containers, state: enabled
Flag: message_containers_deaths_v2, state: enabled
Flag: quorum_queue, state: enabled
Flag: quorum_queue_non_voters, state: enabled
Flag: rabbit_exchange_type_local_random, state: enabled
Flag: rabbitmq_4.0.0, state: enabled
Flag: rabbitmq_4.1.0, state: enabled
Flag: restart_streams, state: enabled
Flag: stream_filtering, state: enabled
Flag: stream_queue, state: enabled
Flag: stream_sac_coordinator_unblock_group, state: enabled
Flag: stream_single_active_consumer, state: enabled
Flag: stream_update_config_command, state: enabled
Flag: tracking_records_in_ets, state: enabled
Flag: user_limits, state: enabled
Flag: virtual_host_metadata, state: enabled
```
```
Cluster status of node rabbit@ubuntu1 ...
Basics

Cluster name: rabbit@ubuntu
Total CPU cores available cluster-wide: 4

Cluster Tags

(none)

Disk Nodes

rabbit@ubuntu
rabbit@ubuntu1

Running Nodes

rabbit@ubuntu
rabbit@ubuntu1

Versions

rabbit@ubuntu1: RabbitMQ 4.1.4 on Erlang 27.3.4.2
rabbit@ubuntu: RabbitMQ 4.1.4 on Erlang 27.3.4.2

CPU Cores

Node: rabbit@ubuntu1, available CPU cores: 2
Node: rabbit@ubuntu, available CPU cores: 2

Maintenance status

Node: rabbit@ubuntu1, status: not under maintenance
Node: rabbit@ubuntu, status: not under maintenance

Alarms

(none)

Network Partitions

(none)

Listeners

Node: rabbit@ubuntu1, interface: [::], port: 15672, protocol: http, purpose: HTTP API
Node: rabbit@ubuntu1, interface: [::], port: 25672, protocol: clustering, purpose: inter-node and CLI tool communication
Node: rabbit@ubuntu1, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0-9-1 and AMQP 1.0
Node: rabbit@ubuntu, interface: [::], port: 15672, protocol: http, purpose: HTTP API
Node: rabbit@ubuntu, interface: [::], port: 25672, protocol: clustering, purpose: inter-node and CLI tool communication
Node: rabbit@ubuntu, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0-9-1 and AMQP 1.0

Feature flags

Flag: classic_mirrored_queue_version, state: enabled
Flag: classic_queue_type_delivery_support, state: enabled
Flag: detailed_queues_endpoint, state: enabled
Flag: direct_exchange_routing_v2, state: enabled
Flag: drop_unroutable_metric, state: enabled
Flag: empty_basic_get_metric, state: enabled
Flag: feature_flags_v2, state: enabled
Flag: implicit_default_bindings, state: enabled
Flag: khepri_db, state: disabled
Flag: listener_records_in_ets, state: enabled
Flag: maintenance_mode_status, state: enabled
Flag: message_containers, state: enabled
Flag: message_containers_deaths_v2, state: enabled
Flag: quorum_queue, state: enabled
Flag: quorum_queue_non_voters, state: enabled
Flag: rabbit_exchange_type_local_random, state: enabled
Flag: rabbitmq_4.0.0, state: enabled
Flag: rabbitmq_4.1.0, state: enabled
Flag: restart_streams, state: enabled
Flag: stream_filtering, state: enabled
Flag: stream_queue, state: enabled
Flag: stream_sac_coordinator_unblock_group, state: enabled
Flag: stream_single_active_consumer, state: enabled
Flag: stream_update_config_command, state: enabled
Flag: tracking_records_in_ets, state: enabled
Flag: user_limits, state: enabled
Flag: virtual_host_metadata, state: enabled

```
![1node](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/RMQ/rmq7.jpg?raw=true)
Для закрепления материала снова запустите скрипт producer.py и приложите скриншот выполнения команды на каждой из нод:

```shell script
$ rabbitmqadmin get queue='hello'
```
![hello](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/RMQ/rmq10.jpg?raw=true)

После чего попробуйте отключить одну из нод, желательно ту, к которой подключались из скрипта, затем поправьте параметры подключения в скрипте consumer.py на вторую ноду и запустите его.

*Приложите скриншот результата работы второго скрипта.*

```
root@ubuntu:/home/w1nce# python3 consumer.py
 [*] Waiting for messages. To exit press CTRL+C
 [x] Received b'Hello Netology!'
 [x] Received b'Hello Netology!'
 [x] Received b'Hello Netology!'
 [x] Received b'Hello Netology!'
 [x] Received b'Hello Netology!'
 [x] Received b'Hello Netology!'
 [x] Received b'Hello Netology!'
 [x] Received b'Hello Netology!'
 [x] Received b'Hello Netology!'

```


