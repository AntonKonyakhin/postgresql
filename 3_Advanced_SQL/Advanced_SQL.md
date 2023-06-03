Задание:
1. Установите postgresql v13 на предоставленной VM c OS Ubuntu 20.04.
```
sudo apt-get update
sudo apt-get install wget ca-certificates
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'

```

```
sudo apt-get update
apt-cache policy postgresql

```
```
sudo apt-get -y install postgresql-13
```

2. Добавьте в PostgreSQL пользователя root c правами SUPERUSER.

```
su postgres
psql
CREATE USER root SUPERUSER;
\du

```

3. Создайте базу данных "rebrain_courses_db".
```
CREATE DATABASE rebrain_courses_db;
```

4. Для работы с базой данных создайте пользователя "rebrain_admin".
```
CREATE ROLE rebrain_admin;
```
```
\du
\l
```
```
\c rebrain_courses_db;
```

5. Выдайте все права пользователю "rebrain_admin" на базу данных "rebrain_courses_db".

```
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public to rebrain_admin;
```
```
GRANT ALL PRIVILEGES ON DATABASE rebrain_courses_db TO rebrain_admin;
```

```
\du
```

```
exit
```

6. Переместите файл бэкапа базы данных "rebrain_courses_db" из предыдущего задания на сервер и положите в директорию /tmp/ с именем rebrain_courses_db.sql.bqp.

```
```

7. Восстановите данные из бэкапа базы данных из предыдущего задания командой: psql -U root -d rebrain_courses_db -f /tmp/rebrain_courses_db.sql.bqp

```
psql -U root -d rebrain_courses_db -f /tmp/rebrain_courses_db.sql.bqp
```

8. C помощью утилиты psql, подключитесь к базе данных "rebrain_courses_db".

```
su postgres
psql -d rebrain_courses_db;

```

9. Проверьте наличие данных в таблицах, чтобы понять, что бекап данных прошел успешно.
```
select * from 
select * from
select * from

```

10. Посчитайте общую стоимость практикумов компании REBRAIN из таблицы courses с помощью оконной функции sum(price) OVER (), результат сохраните в файл /tmp/answers/devops_old_price (используйте запрос SELECT * FROM ...).

```
mkdir /tmp/answers
touch /tmp/answers/devops_old_price
chmod 777 /tmp/answers/devops_old_price
```

```
COPY (SELECT course_id, coursename, price, tasks_count, sum(price) OVER () FROM courses) TO '/tmp/answers/devops_old_price' CSV HEADER;
```

11. Обновите данные цены практикума в таблице №2 для практикума "Devops". Новая цена: 100000 руб.
```
Update courses SET price='100000' where coursename='Devops';
```


12. Посчитайте общую стоимость практикумов компании REBRAIN из обновленной таблицы courses с помощью оконной функции sum(price) OVER (ORDER BY price), результат сохраните в файл /tmp/answers/devops_new_price (используйте запрос SELECT * FROM ...).
```
touch /tmp/answers/devops_new_price
chmod 777 /tmp/answers/devops_new_price
```
```
COPY (SELECT course_id, coursename, price, tasks_count, sum(price) OVER (ORDER BY price) FROM courses) TO '/tmp/answers/devops_new_price' CSV HEADER;
```

13. Добавьте новую таблицу auditlog с NOT NULL полями:
- id (PrimaryKey)
- user_id (id пользователя, который был создан)
- creation_time (время создания записи о новом пользователе)
- creator (имя пользователя базы данных, с помощью которого производился insert)

```
create table auditlog(id serial not null primary key,   -- PrimaryKey
user_id int not null, 									-- id пользователя, который был создан
creation_time timestamp NOT NULL DEFAULT now(),  		-- время создания записи о новом пользователе
creator varchar(50) not null DEFAULT session_user);									-- имя пользователя базы данных, с помощью которого производился insert
```
14. Создайте функцию c именем "fnc_auditlog_users_insert", которая логирует в таблицу (записывает в таблицу) auditlog информацию о регистрации нового пользователя на сайте компании REBRAIN.

```
create or replace function fnc_auditlog_users_insert ()
returns trigger as $$
begin
insert into auditlog(user_id) values (new.user_id);
RETURN NEW;
end;
$$
language plpgsql;


```
```
\df
```

15. Создайте и установите триггер "insert_into_users_trigger" на INSERT данных в таблицу users так, чтобы вызывалась функция c именем "fnc_auditlog_users_insert", которую вы создали выше.

```
create trigger insert_into_users_trigger
after insert on users
for each row
execute function fnc_auditlog_users_insert();

```

16. С помощью команды INSERT, добавьте в таблицу "users" 15 новых пользователей. Для каждого нового пользователя делайте отдельный запрос, так чтобы срабатывал установленный триггер.
```
insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('baba',	'baba.baba@gmail.com',	'+79817937545',	'baba',	'bbb',	'NULL',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.white@gmail.com',	'+79817937555',	'bulka',	'white',	'NULL',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.black@gmail.com',	'+79817937655',	'bulka',	'black',	'NULL',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.yellow@gmail.com',	'+79817934655',	'bulka',	'yellow',	'NULL',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.orange@gmail.com',	'+79817934685',	'bulka',	'orange',	'NULL',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.pink@gmail.com',	'+79917934685',	'bulka',	'pink',	'NULL',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.brown@gmail.com',	'+79917934685',	'bulka',	'brown',	'Moscow',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.silver@gmail.com',	'+79917934685',	'bulka',	'silver',	'Tambov',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.blue@gmail.com',	'+79917934685',	'bulka',	'blue',	'Tambov',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.green@gmail.com',	'+79917934685',	'bulka',	'green',	'Tambov',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.sun@gmail.com',	'+79917934685',	'bulka',	'sun',	'Tambov',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.magenta@gmail.com',	'+79917934685',	'bulka',	'magenta',	'Tambov',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('bulka',	'bulka.gray@gmail.com',	'+79917934685',	'bulka',	'gray',	'Tambov',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('sharik',	'sharik.gray@gmail.com',	'+79917934685',	'sharik',	'gray',	'Tambov',	'false');

insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('tuzik',	'tuzik.gray@gmail.com',	'+79917934685',	'tuzik',	'gray',	'Tambov',	'false');


```

17. Создайте представление c именем "get_last_10_records_from_auditlog", которое позволит вывести из таблицы auditlog последних 10 попыток записи в таблицу users за последний день с сортировкой по времени (select * from auditlog limit 10 сортировка по времени).
```
create view get_last_10_records_from_auditlog
as
select * from auditlog  order by creation_time limit 10;
```

```
select * from get_last_10_records_from_auditlog;
```

18. Сделайте бекап базы с помощью команды: pg_dump -U root rebrain_courses_db > /tmp/rebrain_courses_db_task03.sql.bqp
```
pg_dump -U root rebrain_courses_db > /tmp/rebrain_courses_db_task03.sql.bqp
```
Если уверены, что все выполнили правильно, оправляйте задание на проверку.
