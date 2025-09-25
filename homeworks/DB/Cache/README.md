# Домашнее задание к занятию «Кеширование Redis/memcached» - Винцентини С.Г.

---

### Задание 1. Кеширование 

Приведите примеры проблем, которые может решить кеширование. 

1. Медленное получение данных
2. Высокая нагрузка на сервер базы данных
3. Частые повторяющиеся вычисления
4. Проблемы масштабируемости
5. Непостоянство сетевых соединений

---

### Задание 2. Memcached

Установите и запустите memcached.

*Приведите скриншот systemctl status memcached, где будет видно, что memcached запущен.*

![systemctl status memcached](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/Cache/memcached.jpg?raw=true)

---

### Задание 3. Удаление по TTL в Memcached

Запишите в memcached несколько ключей с любыми именами и значениями, для которых выставлен TTL 5. 

*Приведите скриншот, на котором видно, что спустя 5 секунд ключи удалились из базы.*

![telnet localhost 11211](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/Cache/memcachedttl5.jpg?raw=true)

---

### Задание 4. Запись данных в Redis

Запишите в Redis несколько ключей с любыми именами и значениями. 

*Через redis-cli достаньте все записанные ключи и значения из базы, приведите скриншот этой операции.*

![redis-cli](https://github.com/w1ncez/myfirstrepo/blob/main/homeworks/DB/Cache/rediskeys.jpg?raw=true)
