# Домашнее задание к занятию "6.2. SQL"". 

***Задача 1***

***Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.***

Ответ - 

docker pull postgres:12

docker volume create vol2

vol2

docker volume create vol1

vol1

docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol1:/var/lib/postgresql/data -v vol2:/var/lib/postgresql postgres:12

docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol1:/var/lib/postgresql/data -v vol2:/var/lib/postgresql postgres:12

![ss_6_1](https://user-images.githubusercontent.com/95530808/187216736-7af2e52a-097a-4dfe-bb7b-11707ce9de06.png)

***Задача 2***

***В БД из задачи 1:***

***создайте пользователя test-admin-user и БД test_db в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
создайте пользователя test-simple-user предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db***

Ответ - 

Команды:
```
CREATE DATABASE test_db

CREATE ROLE "test-admin-user" SUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;

CREATE TABLE orders

(

id integer,

name text,

price integer,

PRIMARY KEY (id) 

);

CREATE TABLE clients 

(

	id integer PRIMARY KEY,
  
	lastname text,
  
	country text,
  
	booking integer,
  
	FOREIGN KEY (booking) REFERENCES orders (Id)
  
);

CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;

GRANT SELECT ON TABLE public.clients TO "test-simple-user";

GRANT INSERT ON TABLE public.clients TO "test-simple-user";

GRANT UPDATE ON TABLE public.clients TO "test-simple-user";

GRANT DELETE ON TABLE public.clients TO "test-simple-user";

GRANT SELECT ON TABLE public.orders TO "test-simple-user";

GRANT INSERT ON TABLE public.orders TO "test-simple-user";

GRANT UPDATE ON TABLE public.orders TO "test-simple-user";

GRANT DELETE ON TABLE public.orders TO "test-simple-user";

```

Скриншоты 

Список БД:

![ss_6_2_0](https://user-images.githubusercontent.com/95530808/187217572-126e6683-9ab6-48c7-a46b-a97c0c750e87.png)

Список Таблиц и привелегий:


![ss_6_2_1](https://user-images.githubusercontent.com/95530808/187217806-4ec2b00e-4540-4268-ac42-0cfc7fdce881.png)


Скрины из Beaver:

![ss_6_2_2](https://user-images.githubusercontent.com/95530808/187217940-1562316b-01da-4e48-b1ba-e4d3cdde4451.png)

![ss_6_2_3](https://user-images.githubusercontent.com/95530808/187217967-beea530d-8c87-4709-80f8-3e5eaf4355e6.png)

***Задача 3***

***Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:***

Ответ - 
```
insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);

insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');

select count (*) from orders;

select count (*) from clients;

```

![ss_6_3_1](https://user-images.githubusercontent.com/95530808/187218768-4f9d9000-440b-4a4f-9ee3-def22512030d.png)

***Задача 4***

***Часть пользователей из таблицы clients решили оформить заказы из таблицы orders. Используя foreign keys свяжите записи из таблиц, согласно таблице:***

Ответ - 
```
update  clients set booking = 3 where id = 1;

update  clients set booking = 4 where id = 2;

update  clients set booking = 5 where id = 3;

Запрос получения Вариант1 -  учитывает наличие в таблице заказов данных:

select * from clients as c where  exists (select id from orders as o where c.booking = o.id) ;

Запрос получения Вариант2 - проверяет на непустые значения у клиентов по заказам:

select * from clients where booking is not null

```

![ss_6_4_1](https://user-images.githubusercontent.com/95530808/187220494-e0ba1cdb-cdfd-4763-b719-ce0f513a4a1f.png)


***Задача 5***

***Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN). Приведите получившийся результат и объясните что значат полученные значения.***

![ss_6_5_1](https://user-images.githubusercontent.com/95530808/187220996-5e38d0a5-e9cd-4fcc-a20c-0a6a0d15cead.png)

Вариант 1

Показывает стоимость(нагрузку на исполнение) запроса (не оптимальный в сравнении с Вар2)

Показывает шаги связи, и сбор сканирование таблиц после связи

Вариант 2

Так же показывает стоимость(нагрузку на исполнение) запроса , и фильтрацию по полю Booking для выборки.

По обоим планам видно что Вар2 оптимальней. 

***Задача 6***

***Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).***

***Остановите контейнер с PostgreSQL (но не удаляйте volumes). Поднимите новый пустой контейнер с PostgreSQL.
Восстановите БД test_db в новом контейнере. Приведите список операций, который вы применяли для бэкапа данных и восстановления.***

 Ответ -
 ```
 docker exec -t pgre-docker pg_dump -U postgres test_db -f /var/lib/postgresql/data/dump_test.sql

 docker exec -i pgre-docker2 psql -U postgres -d test_db -f /var/lib/postgresql/data/dump_test.sql
SET
SET
SET
SET
SET
 set_config 
------------
 
(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE TABLE
ALTER TABLE
COPY 5
COPY 5
ALTER TABLE
ALTER TABLE
ALTER TABLE
GRANT
GRANT
 
```

Второй контейнер поднят на порту 5433

![ss_6_6_1](https://user-images.githubusercontent.com/95530808/187221694-49bec553-36cc-4394-ba76-293195075e49.png)
