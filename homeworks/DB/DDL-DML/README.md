# Домашнее задание к занятию «Работа с данными (DDL/DML)» - Винцентини С.Г.
---

Задание можно выполнить как в любом IDE, так и в командной строке.

### Задание 1
1.1. Поднимите чистый инстанс MySQL версии 8.0+. Можно использовать локальный сервер или контейнер Docker.

1.2. Создайте учётную запись sys_temp. 

1.3. Выполните запрос на получение списка пользователей в базе данных. (скриншот)

1.4. Дайте все права для пользователя sys_temp. 

1.5. Выполните запрос на получение списка прав для пользователя sys_temp. (скриншот)

1.6. Переподключитесь к базе данных от имени sys_temp.

Для смены типа аутентификации с sha2 используйте запрос: 
```sql
ALTER USER 'sys_test'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
1.6. По ссылке https://downloads.mysql.com/docs/sakila-db.zip скачайте дамп базы данных.

1.7. Восстановите дамп в базу данных.

1.8. При работе в IDE сформируйте ER-диаграмму получившейся базы данных. При работе в командной строке используйте команду для получения всех таблиц базы данных. (скриншот)

*Результатом работы должны быть скриншоты обозначенных заданий, а также простыня со всеми запросами.*

----------
![ddldml1](https://github.com/user-attachments/assets/6096506f-d945-4944-ba66-df9e93faa03d)
![ddldml2](https://github.com/user-attachments/assets/2a08689c-faf3-43a3-8df6-baebfc56db8a)
![ddldml3](https://github.com/user-attachments/assets/f0cec4dd-86cc-4e5a-985e-9bf0d9a5a12a)
```
CREATE USER 'sys_temp'@'localhost' IDENTIFIED BY 'password';

SELECT User, Host FROM mysql.user;

GRANT ALL PRIVILEGES ON *.* TO 'sys_temp'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;

SHOW GRANTS FOR 'sys_temp'@'localhost';

ALTER USER 'sys_temp'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
FLUSH PRIVILEGES;

CREATE DATABASE sakila;
USE sakila;

mysql -u sys_temp -p sakila < sakila-db/sakila-schema.sql
mysql -u sys_temp -p sakila < sakila-db/sakila-data.sql

USE sakila;
SHOW TABLES;
```

### Задание 2
Составьте таблицу, используя любой текстовый редактор или Excel, в которой должно быть два столбца: в первом должны быть названия таблиц восстановленной базы, во втором названия первичных ключей этих таблиц. Пример: (скриншот/текст)
```
Название таблицы | Название первичного ключа
customer         | customer_id
```
----------
```sql
+----------------------------+----------------------------+
| Название таблицы           | Название первичного ключа  |
+----------------------------+----------------------------+
| actor                      | actor_id                   |
| address                    | address_id                 |
| category                   | category_id                |
| city                       | city_id                    |
| country                    | country_id                 |
| customer                   | customer_id                |
| film                       | film_id                    |
| film_actor                 | actor_id, film_id          |
| film_category              | film_id, category_id       |
| film_text                  | film_id                    |
| inventory                  | inventory_id               |
| language                   | language_id                |
| payment                    | payment_id                 |
| rental                     | rental_id                  |
| staff                      | staff_id                   |
| store                      | store_id                   |
+----------------------------+----------------------------+
```
