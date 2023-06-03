1. Установите postgresql v13 на VM c OS Ubuntu 20.04.

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

6. C помощью утилиты psql, подключитесь к базе данных "rebrain_courses_db".


```
su postgres
psql -d rebrain_courses_db;

```



таблица 1
```
CREATE TABLE users(
    user_id SERIAL PRIMARY KEY NOT NULL,             -- Primary Key
    username varchar(50) NOT NULL,                   -- Имя пользователя
    email varchar(50) NOT NULL,                      -- Электронная почта
    mobile_phone varchar(12) NOT NULL,               -- Номер телефона
    firstname TEXT NOT NULL,                         -- Имя
    lastname TEXT NOT NULL,                          -- Фамилия
    city  TEXT,                                      -- Название города
    is_curator boolean NOT NULL,                     -- Является ли пользователь куратором
    record_date timestamp NOT NULL DEFAULT now()     -- Время создания записи о пользователе
    );
	
```
таблица 2
```
CREATE TABLE courses(
    course_id SERIAL PRIMARY KEY NOT NULL,  -- Primary Key
    coursename varchar(50) NOT NULL,        -- Название практикума
    tasks_count INT NOT NULL,               -- Количество заданий в практикуме
    price INT NOT NULL                      -- Цена практикума
    );
```

```
CREATE TABLE users__courses(
    id SERIAL PRIMARY KEY NOT NULL,     -- Primary Key
    user_id INT NOT NULL,               -- Foreign Key to table users 
    course_id INT NOT NULL,             -- Foreign Key to table courses 
    CONSTRAINT fk_user_id
        FOREIGN KEY (user_id) 
            REFERENCES users(user_id),
    CONSTRAINT fk_course_id
        FOREIGN KEY (course_id) 
            REFERENCES courses(course_id)
    );
```






```
insert into users (username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values 
('admin',	'vasiliy_ozerov@mail.com',	'+79111937483',	'Vasiliy',	'Ozerov',	'Moscow',	'true'),
('pricex',	'SimonWels@mail.com',	'+79111974384',	'Simon',	'Wels',	'London',	'false'),
('MaytheForcebewithyou',	'Leya.Organa@newrepublic.com',	'+79991916526',	'Leya',	'Organa',	'Alderaan',	'false'),
('yodajedi',	'magistr_yoda@oldrepublic.com',	'+79114326786',	'Yoda',	'Yodin',	'Dagoba',	'false'),
('herion1',	'Sabur03@yandex.ru',	'+79110983475',	'Sabur',	'Gagiev',	'Kazan',	'false'),
('pusha',	'Liliana_Sal@yandex.ru',	'+79115897438',	'Liliana',	'Salohina',	'SPB',	'false'),
('vierjohn',	'xen_power@mail.com',	'+79112232134',	'John',	'Seltic',	'NULL',	'false'),
('trakstars',	'Pronina@mail.ru',	'+79811987483',	'Varya',	'Pronina',	'Moscow',	'false'),
('vozilla',	'Maxxx@mail.ru',	'+79111047854',	'Max',	'Visikov',	'NULL',	'false'),
('Kisa007',	'Kisa007@mail.ru',	'+79112405972',	'Raya',	'Simonova',	'Tver',	'false'),
('greenball',	'maike.greenball.vazovsky@corp.com',	'+79112425872',	'Mike',	'Vazovsky',	'Monstertown',	'false');
```





```
insert into courses (coursename,	tasks_count,	price) values
('Kubernetes',	'70',	'35000'),
('Highload',	'130',	'75000'),
('Bash',	'15',	'6900'),
('Golang',	'117',	'55000'),
('Linux',	'102',	'65000'),
('Devops',	'212',	'75000'),
('Docker',	'45',	'30000'),
('Logs',	'14',	'7900');
```

```
insert into users__courses (user_id,	course_id) values
('1',	'1'),
('1',	'4'),
('3',	'2'),
('3',	'4'),
('2',	'6'),
('2',	'1'),
('2',	'2'),
('2',	'4'),
('5',	'6'),
('6',	'6');

```



Внесите информацию о новом пользователе в таблицу №1:
```
insert into users(username,	email,	mobile_phone,	firstname,	lastname,	city,	is_curator) values
('vladon',	'Vladislav.Pirushin@gmail.com',	'+79817937545',	'Vladislav',	'Pirushin',	'NULL',	'false');
```

Внесите информацию о новом курсе "Postgresql" в таблицу №2
```
insert into courses (coursename,	tasks_count,	price) values 
('Postgresql',	'14',	'7900');
```

Внесите в таблицу №3 данные о том, что пользователь c номером мобильного телефона "+79991916526" купил практикум "Devops".
```
select user_id from users where mobile_phone='+79991916526';
```
```
select course_id from courses where coursename='Devops';
```

```
insert into users__courses (user_id,	course_id) values
('3',	'6');
```

Получите все данные из таблицы №2 c информацией о курсах, результат сохраните в файл /tmp/answers/table2_courses_data. Для сохранения результата запроса в файл используйте запрос "COPY (SELECT..) TO /path/to/file CSV HEADER;", psql метакоманду "\o<\b> /path/to/file SELECT ... " или OS команду "psql -o /path/to/file". Проверяйте в выведенных данных наличие заголовков полей (это важно для успешного прохождения автопроверок).


```
COPY (SELECT * FROM courses) TO '/tmp/answers/table2_courses_data' CSV HEADER;
```

Получите из таблицы №1 список имен пользователей (username) и их мобильных номеров (mobile_phone), результат сохраните в файл /tmp/answers/table1_usernames_and_phones.

```
COPY (Select username, mobile_phone from users) TO '/tmp/answers/table1_usernames_and_phones' CSV HEADER;
```


Удалите все данные из таблицы №1, связанные с именем пользователя "yodajedi".
```
delete from users where username = 'yodajedi';
```


Обновите данные цены практикума в таблице №2 для практикума "Postgresql". Новая цена: 10000 тыс. руб.
```
Update courses SET price='10000' where coursename='Postgresql';
```

Обновите данные пользователя "Vladislav Pirushin" в таблице №1 указав, что он теперь является куратором.
```
Update users SET is_curator='true' where firstname='Vladislav' and lastname='Pirushin';
```

Используя LEFT OUTER JOIN получите всю информацию (SELECT *) из таблицы №2 и таблицы №3, результат сохраните в файл /tmp/answers/LEFT_OUTER_JOIN в формате CSV с заголовками.

```
COPY (select * from courses u left join users__courses uc on u.course_id=uc.course_id) TO '/tmp/answers/LEFT_OUTER_JOIN' CSV HEADER;
```


Используя RIGHT OUTER JOIN получите всю информацию (SELECT *) из таблицы №1 и таблицы №3, результат сохраните в файл /tmp/answers/RIGHT_OUTER_JOIN в формате CSV с заголовками.

```
COPY (select * from users u left join users__courses uc on u.user_id=uc.user_id) TO '/tmp/answers/RIGHT_OUTER_JOIN' CSV HEADER;
```


Сделайте бэкап базы данных командой: pg_dump -U root rebrain_courses_db > rebrain_courses_db.sql.bqp

```

```

Сохраните файл бэкапа базы данных rebrain_courses_db.sql.bqp к себе на компьютер для выполнения следующих заданий.
