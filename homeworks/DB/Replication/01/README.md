# Домашнее задание к занятию «Репликация и масштабирование. Часть 1»  - Винцентини С.Г.

### Задание 1

На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.

--

Master-Slave репликация - это простая и надёжная схема, где только один master сервер принимает запись, а остальные slaves сервера копируют его данные и отвечают за чтение. Это отлично подходит для масштабирования нагрузки на чтение, резервного копирования и базовой отказоустойчивости. Однако запись остаётся узким местом, а при падении master’а требуется failover.
Master-Master репликация - оба сервера могут и читать, и писать, автоматически синхронизируя изменения друг с другом. Это даёт высокую доступность и распределение нагрузки на запись, но сильно усложняет систему: возможны конфликты данных, циклы репликации и расхождения, если что-то пойдёт не так. Такая схема требует тщательной настройки и поддержки со стороны СУБД.
В итоге: master-slave - про простоту и стабильность, master-master - про гибкость и отказоустойчивость ценой сложности.

---

### Задание 2

Выполните конфигурацию master-slave репликации, примером можно пользоваться из лекции.

*Приложите скриншоты конфигурации, выполнения работы: состояния и режимы работы серверов.*

--

Рассмотрим пример развертывания 2-ух монолитных инстансов PostgreSQL,

vbox1 - 192.168.1.3 - master нода

vbox2 - 192.168.1.10 - slave нода

#### Действия на мастере
Для начала установим PostgreSQL 
```bash
sudo apt update
sudo apt install postgresql
```
Подключмся и создадим пользователя *replicator*
```bash
sudo -u postgres psql
```
```sql
CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD 'replicapass';
\q
```
Отредактируем конфигурационный файл по пути */etc/postgresql/16/main/postgresql.conf*:
```
# Разрешить подключения со всех адресов
listen_addresses = '*'
# Уровень WAL — обязательно replica
wal_level = replica
# Макс. число отправщиков WAL
max_wal_senders = 3
# Макс. число одновременных подключений
max_connections = 100
# Включить горячий standby на реплике
hot_standby = on
```
Отредактируем конфигурационный файл по пути */etc/postgresql/16/main/pg_hba.conf*, добавив в конец:
```
host    replication     replicator      192.168.1.10/32         md5
```
Перезапустим сервис postgresql@16-main и проверим его статус
```bash
sudo systemctl restart postgresql@16-main 
sudo systemctl status postgresql@16-main 
```
#### Действия на слэйве
Установим PostgreSQL, перед этим маскировав сервис (нам не нужен автоматический запуск)
```bash
sudo systemctl mask postgresql@16-main 
sudo apt update
sudo apt install postgresql
sudo systemctl unmask postgresql@16-main 
```
Выполняем команду *pg_basebackup* и вводим ранее заданный пароль
```bash
sudo -u postgres pg_basebackup -h 192.168.1.3 -p 5432 -U replicator -D /var/lib/postgresql/16/main -P -R -X stream -C --slot=replica_slot_1
```
После этого проверяем, что создался *standby.signal*
```bash
ls -l /var/lib/postgresql/16/main/standby.signal
```
Запускаем сервис *postgresql@16-main* и проверяем его статус
```bash
sudo systemctl start postgresql@16-main
sudo systemctl status postgresql@16-main
```

#### Проверка репликации

На слэйве:
```
sudo -u postgres psql -c "SELECT pg_is_in_recovery();"
```
![](slave_repl1)
На мастере:
```
sudo -u postgres psql -c "SELECT * FROM pg_stat_replication;"
```
![](master_repl1)
```
sudo -u postgres psql -c "SELECT * FROM pg_replication_slots;"
```
![](master_repl2)

Произведем тестовую вставку данныж на мастере:

```bash
sudo -u postgres psql -c "CREATE TABLE IF NOT EXISTS test_replica (id SERIAL, msg TEXT);" 
sudo -u postgres psql -c "INSERT INTO test_replica (msg) VALUES ('Hello from master at ' || NOW());"
```
![](master_insert1)

Проверим данные на слэйве:

```bash
sudo -u postgres psql -c "SELECT * FROM test_replica;"
```
![](slave_answer1)
