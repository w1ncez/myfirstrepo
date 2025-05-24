# Домашнее задание к занятию «Docker. Часть 2 - 'Винцентини Сергей'

---

### Задание 1

**Напишите ответ в свободной форме, не больше одного абзаца текста.**

Установите Docker Compose и опишите, для чего он нужен и как может улучшить лично вашу жизнь.

*Docker Compose был установлен ранее вместе с Dosker Engine, проверка его установки осуществляется командой*  
```
docker compose version 
```
*Вывод команды:*  
```
docker-compose version 1.29.2, build unknown
docker-py version: 5.0.3
CPython version: 3.12.3
OpenSSL version: OpenSSL 3.0.13 30 Jan 2024
```  

*Docker compose делает мою жизнь лучше благодаря возможности централизованно контролировать зависимые контейнеры, сохраняя консистентность среды разработки и продуктивной инфраструктуры. Вместо запуска отдельных контейнеров вручную, я могу описать всё необходимое в одном файле , а затем одним действием автоматически развернуть и сконфигурировать инфраструктуру.*

---

### Задание 2 

**Выполните действия и приложите текст конфига на этом этапе.** 

Создайте файл docker-compose.yml и внесите туда первичные настройки: 

 * version;
 * services;
 * volumes;
 * networks.


При выполнении задания используйте подсеть 10.5.0.0/16.
Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw.
Все приложения из последующих заданий должны находиться в этой конфигурации.


```
version: '3.3'

services:
volumes:
networks:
  vintsentinisg-my-netology-hw
    driver: bridge
     ipam:
      config:
      - subnet: 10.5.0.0/16
      - gateway: 10.5.0.1
```

---

### Задание 3 

**Выполните действия:** 

1. Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus. 
2. Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории [6-04/prometheus](https://github.com/netology-code/sdvps-homeworks/tree/main/lecture_demos/6-04/prometheus) ).
3. Обеспечьте внешний доступ к порту 9090 c докер-сервера.

```

services:
  prometheus:
    image: prom/prometheus:v2.54.1
    container_name: vintsentinisg-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
    - 9090:9090
    volumes:
    - ./prometheus:/etc/prometheus
    - prometheus-data:/prometheus
volumes:
   prometheus-data:

```

---

### Задание 4 

**Выполните действия:**

1. Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway. 
2. Обеспечьте внешний доступ к порту 9091 c докер-сервера.

```

services:
  pushgateway:
    image: prom/pushgateway:v1.9.0
    container_name: vintsentinisg-netology-pushgateway
    ports:
    - 9091:9091
    
```


---

### Задание 5 

**Выполните действия:** 

1. Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana. 
2. Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории [6-04/grafana](https://github.com/netology-code/sdvps-homeworks/blob/main/lecture_demos/6-04/grafana/custom.ini).
3. Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
4. Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.

```

services:
  grafana:
    image: grafana/grafana:11.2.0
    container_name: vintsentinisg-netology-grafana
    environment:
     GF_PATHS_CONFIG: /etc/grafana/custom.ini
    ports:
    - 80:3000
    volumes:
    - ./grafana:/etc/grafana
    - grafana-data:/var/lib/grafana
volumes:
  grafana-data:

```


---

### Задание 6 

**Выполните действия.**

1. Настройте поочередность запуска контейнеров.
2. Настройте режимы перезапуска для контейнеров.
3. Настройте использование контейнерами одной сети.
5. Запустите сценарий в detached режиме.

```
services:
  prometheus:
    networks:
    - vintsentinisg-my-netology-hw
    restart: always
  pushgateway:
    networks:
    - vintsentinisg-my-netology-hw
    depends_on:
    - prometheus
    restart: unless-stopped
  grafana:
    networks:
    - vintsentinisg-my-netology-hw
    depends_on:
    - prometheus
    restart: unless-stopped

```
![docker ps](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/docker-part2/Screenshot%20from%202025-05-24%2019-47-59.png?raw=true)



---

### Задание 7 

**Выполните действия.**
1. Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: ```echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology```.
2. Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
3. Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
4. Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.

В качестве решения приложите:

* docker-compose.yml **целиком**;
* скриншот команды docker ps после запуске docker-compose.yml;
* скриншот графика, постоенного на основе вашей метрики.

```
version: '3.3'

services:
  prometheus:
    image: prom/prometheus:v2.54.1
    container_name: vintsentinisg-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
    - 9090:9090
    volumes:
    - ./prometheus:/etc/prometheus
    - prometheus-data:/prometheus
    networks:
    - vintsentinisg-my-netology-hw
    restart: always
  pushgateway:
    image: prom/pushgateway:v1.9.0
    container_name: vintsentinisg-netology-pushgateway
    ports:
    - 9091:9091
    networks:
    - vintsentinisg-my-netology-hw
    depends_on:
    - prometheus
    restart: unless-stopped
  grafana:
    image: grafana/grafana:11.2.0
    container_name: vintsentinisg-netology-grafana
    environment:
     GF_PATHS_CONFIG: /etc/grafana/custom.ini
    ports:
    - 80:3000
    volumes:
    - ./grafana:/etc/grafana
    - grafana-data:/var/lib/grafana
    networks:
    - vintsentinisg-my-netology-hw
    depends_on:
    - prometheus
    restart: unless-stopped
volumes:
  prometheus-data:
  grafana-data:
networks:
 vintsentinisg-my-netology-hw:
  driver: bridge
  ipam:
   config:
   - subnet: 10.5.0.0/16
```

![docker ps](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/docker-part2/Screenshot%20from%202025-05-24%2019-50-49.png?raw=true)
![grafana dashboard](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/docker-part2/Screenshot%20from%202025-05-24%2019-56-48.png?raw=true)
---

### Задание 8

**Выполните действия:** 

1. Остановите и удалите все контейнеры одной командой.

В качестве решения приложите скриншот консоли с проделанными действиями.

![docker compose down](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/docker-part2/Screenshot%20from%202025-05-24%2020-02-20.png?raw=true)

---
