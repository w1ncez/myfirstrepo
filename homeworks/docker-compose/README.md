
# Домашнее задание к занятию 4 «Оркестрация группой Docker контейнеров на примере Docker Compose» - Винцентини С.Г.

## Задача 1

Сценарий выполнения задачи:
- Установите docker и docker compose plugin на свою linux рабочую станцию или ВМ.
- Если dockerhub недоступен создайте файл /etc/docker/daemon.json с содержимым: ```{"registry-mirrors": ["https://mirror.gcr.io", "https://daocloud.io", "https://c.163.com/", "https://registry.docker-cn.com"]}```
- Зарегистрируйтесь и создайте публичный репозиторий  с именем "custom-nginx" на https://hub.docker.com (ТОЛЬКО ЕСЛИ У ВАС ЕСТЬ ДОСТУП);
- скачайте образ nginx:1.29.0;
- Создайте Dockerfile и реализуйте в нем замену дефолтной индекс-страницы(/usr/share/nginx/html/index.html), на файл index.html с содержимым:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I will be DevOps Engineer!</h1>
</body>
</html>
```
- Соберите и отправьте созданный образ в свой dockerhub-репозитории c tag 1.0.0 (ТОЛЬКО ЕСЛИ ЕСТЬ ДОСТУП). 
- Предоставьте ответ в виде ссылки на https://hub.docker.com/<username_repo>/custom-nginx/general .

--


https://hub.docker.com/repository/docker/w1ncez/custom-nginx


---


## Задача 2
1. Запустите ваш образ custom-nginx:1.0.0 командой docker run в соответвии с требованиями:
- имя контейнера "ФИО-custom-nginx-t2"
- контейнер работает в фоне
- контейнер опубликован на порту хост системы 127.0.0.1:8080
2. Не удаляя, переименуйте контейнер в "custom-nginx-t2"
3. Выполните команду ```date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; docker ps ; ss -tlpn | grep 127.0.0.1:8080  ; docker logs custom-nginx-t2 -n1 ; docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html```
4. Убедитесь с помощью curl или веб браузера, что индекс-страница доступна.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

--

<img width="1874" height="670" alt="image" src="https://github.com/user-attachments/assets/70c231a3-ea1c-4f43-8492-b737093e027c" />


---

## Задача 3
1. Воспользуйтесь docker help или google, чтобы узнать как подключиться к стандартному потоку ввода/вывода/ошибок контейнера "custom-nginx-t2".
2. Подключитесь к контейнеру и нажмите комбинацию Ctrl-C.
3. Выполните ```docker ps -a``` и объясните своими словами почему контейнер остановился.
4. Перезапустите контейнер
5. Зайдите в интерактивный терминал контейнера "custom-nginx-t2" с оболочкой bash.
6. Установите любимый текстовый редактор(vim, nano итд) с помощью apt-get.
7. Отредактируйте файл "/etc/nginx/conf.d/default.conf", заменив порт "listen 80" на "listen 81".
8. Запомните(!) и выполните команду ```nginx -s reload```, а затем внутри контейнера ```curl http://127.0.0.1:80 ; curl http://127.0.0.1:81```.
9. Выйдите из контейнера, набрав в консоли  ```exit``` или Ctrl-D.
10. Проверьте вывод команд: ```ss -tlpn | grep 127.0.0.1:8080``` , ```docker port custom-nginx-t2```, ```curl http://127.0.0.1:8080```. Кратко объясните суть возникшей проблемы.
11. * Это дополнительное, необязательное задание. Попробуйте самостоятельно исправить конфигурацию контейнера, используя доступные источники в интернете. Не изменяйте конфигурацию nginx и не удаляйте контейнер. Останавливать контейнер можно. [пример источника](https://www.baeldung.com/linux/assign-port-docker-container)
12. Удалите запущенный контейнер "custom-nginx-t2", не останавливая его.(воспользуйтесь --help или google)

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

--

Команда docker attach подключает к главному процессу контейнера (PID 1).
При нажатии Ctrl+C отправляется сигнал SIGINT этому процессу и контейнер останавливается.

<img width="1422" height="1035" alt="image" src="https://github.com/user-attachments/assets/e1b12397-41c4-4eae-8ca2-c9b2204119d4" />
<img width="1571" height="928" alt="image" src="https://github.com/user-attachments/assets/bdcef65e-4374-455a-a52b-acce47ad4646" />
<img width="1084" height="287" alt="image" src="https://github.com/user-attachments/assets/33b92ab1-e726-45fc-8d25-e52e379d8515" />
<img width="1053" height="124" alt="image" src="https://github.com/user-attachments/assets/a8f00391-fe87-44c5-aad1-865a00280a93" />

Проблема с недоступностью порта 8080 означает, что Docker пробрасывает порт 80 контейнера на 8080 хоста, но nginx теперь слушает порт 81 внутри контейнера.

<img width="900" height="1148" alt="image" src="https://github.com/user-attachments/assets/fa3e23c3-7437-4a06-95e6-0780dc4dc82b" />

---

## Задача 4


- Запустите первый контейнер из образа ***centos*** c любым тегом в фоновом режиме, подключив папку  текущий рабочий каталог ```$(pwd)``` на хостовой машине в ```/data``` контейнера, используя ключ -v.
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив текущий рабочий каталог ```$(pwd)``` в ```/data``` контейнера. 
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```.
- Добавьте ещё один файл в текущий каталог ```$(pwd)``` на хостовой машине.
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.


В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

--

<img width="734" height="109" alt="image" src="https://github.com/user-attachments/assets/1f86099c-7957-4995-bea4-da11b13df53b" />
<img width="725" height="112" alt="image" src="https://github.com/user-attachments/assets/fe581327-da20-434e-baf8-292704a13524" />
<img width="1122" height="150" alt="image" src="https://github.com/user-attachments/assets/86311894-e273-4c1c-8fdb-7abee76f95df" />
<img width="657" height="256" alt="image" src="https://github.com/user-attachments/assets/e5d6c65e-6a15-4122-91a1-b30ab1fa76a0" />
<img width="373" height="47" alt="image" src="https://github.com/user-attachments/assets/5c1c2238-7f94-457d-81ef-10444b359384" />


---

## Задача 5

1. Создайте отдельную директорию(например /tmp/netology/docker/task5) и 2 файла внутри него.
"compose.yaml" с содержимым:
```
version: "3"
services:
  portainer:
    network_mode: host
    image: portainer/portainer-ce:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```
"docker-compose.yaml" с содержимым:
```
version: "3"
services:
  registry:
    image: registry:2

    ports:
    - "5000:5000"
```

И выполните команду "docker compose up -d". Какой из файлов был запущен и почему? (подсказка: https://docs.docker.com/compose/compose-application-model/#the-compose-file )

2. Отредактируйте файл compose.yaml так, чтобы были запущенны оба файла. (подсказка: https://docs.docker.com/compose/compose-file/14-include/)

3. Выполните в консоли вашей хостовой ОС необходимые команды чтобы залить образ custom-nginx как custom-nginx:latest в запущенное вами, локальное registry. Дополнительная документация: https://distribution.github.io/distribution/about/deploying/
4. Откройте страницу "https://127.0.0.1:9000" и произведите начальную настройку portainer.(логин и пароль адмнистратора)
5. Откройте страницу "http://127.0.0.1:9000/#!/home", выберите ваше local  окружение. Перейдите на вкладку "stacks" и в "web editor" задеплойте следующий компоуз:

```
version: '3'

services:
  nginx:
    image: 127.0.0.1:5000/custom-nginx
    ports:
      - "9090:80"
```
6. Перейдите на страницу "http://127.0.0.1:9000/#!/2/docker/containers", выберите контейнер с nginx и нажмите на кнопку "inspect". В представлении <> Tree разверните поле "Config" и сделайте скриншот от поля "AppArmorProfile" до "Driver".

7. Удалите любой из манифестов компоуза(например compose.yaml).  Выполните команду "docker compose up -d". Прочитайте warning, объясните суть предупреждения и выполните предложенное действие. Погасите compose-проект ОДНОЙ(обязательно!!) командой.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод, файл compose.yaml , скриншот portainer c задеплоенным компоузом.

--

<img width="875" height="343" alt="image" src="https://github.com/user-attachments/assets/c73781e0-35b5-4785-8767-dec3290699fb" />
Согласно официальной документации, приоритет выше у файла *compose.yaml*. Файл *docker-compose.yaml* в данном случае игнорируется.
<img width="1197" height="261" alt="image" src="https://github.com/user-attachments/assets/9b4def91-80b1-42a0-8493-32a8e8605722" />
<img width="990" height="329" alt="image" src="https://github.com/user-attachments/assets/dfd7b790-facd-4233-8600-490f8fd2a225" />
<img width="1528" height="294" alt="image" src="https://github.com/user-attachments/assets/2b96001f-d154-4d02-a99d-ec403b07c6b9" />
<img width="950" height="1087" alt="image" src="https://github.com/user-attachments/assets/2411e142-a69c-4246-9268-d3e7037e46b4" />
<img width="1850" height="82" alt="image" src="https://github.com/user-attachments/assets/00c9025f-5fcd-4b01-9b52-8eebb0ad0e73" />
<img width="1866" height="98" alt="image" src="https://github.com/user-attachments/assets/c226a992-48f5-4849-a881-072adabdcc0d" />
<img width="1484" height="110" alt="image" src="https://github.com/user-attachments/assets/af9be8b1-8644-424f-908c-ba3f87fd66d7" />
По умолчанию Docker не удаляет контейнеры, которых нет в текущем файле compose и они остаются "сиротами".









---
