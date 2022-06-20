# Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера. 

***Задача 1***

Сценарий выполения задачи:

создайте свой репозиторий на https://hub.docker.com;

выберете любой образ, который содержит веб-сервер Nginx;

создайте свой fork образа;

реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:

```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.

Ответ - https://hub.docker.com/repository/docker/step4j/nginx

***Задача 2
Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"***


Сценарий:

Высоконагруженное монолитное java веб-приложение;

Nodejs веб-приложение;

Мобильное приложение c версиями для Android и iOS;

Шина данных на базе Apache Kafka;

Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;

Мониторинг-стек на базе Prometheus и Grafana;

MongoDB, как основное хранилище данных для java-приложения;

Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.

Ответ - 

Высоконагруженное монолитное java веб-приложение - Считаю если приложение высоконагруженное то контейниразиция не подойдёт, смотрел бы больше в сторону физического сервера или виртуальной.

Nodejs веб-приложение - Контейнеризация подойдёт, не думаю что это приложение вызовет большую нагрузку.

Мобильное приложение c версиями для Android и iOS - Если это приложение то скорее всего им будут пользоваться другие люди, считаю что контейнеризация не подойдёт, смотрел бы в сторону ВМ.

Шина данных на базе Apache Kafka - По моему мнению хорошо применить контейнеризацию, так как отсутствуют накладные расходы на виртуализацию.

Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana - для упомянутых продуктов есть контейнеры на docker hub. Из-за простоты управления и сборки контейнеров, мне кажется необходимо распихать продукты по контейнерам и на основании контейнеров собрать кластер стека ELK. В силу прозрачности реализации, в том числе возможности реализации подходов IaaC, контейнеризация в данном случае помогает закрыть вопросы по менеджменту и что очень важно получить регулярный предсказуемый результат.

Мониторинг-стек на базе Prometheus и Grafana - Контейнериация подойдёт, думаю что часто будет меняться какая то информация так как это мониторинг система, как раз можно будет делать разные версии применяю теги.

MongoDB, как основное хранилище данных для java-приложения - либо виртуализация, либо контейнеризация, все зависит от реализации архитектуры приложения. Никогда не работал с данной БД, затрудняюсь обосновать выбор. Чувствую, что так будет правильно.

Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry - отдельный физический сервер или виртуализация, если сервер есть в наличии использовал бы его.


***Задача 3***

Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;

Запустите второй контейнер из образа debian в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;

Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data;

Добавьте еще один файл в папку /data на хостовой машине;

Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера.

Ответ - 

Подымаем первый контейнер, так как в контейнере определена команда CMD bash, то при запуске выполняется запуск bash и контейнер уходит в статус stop, поэтому применяю sleep infinity, можно было использовать ключ -t. При помощи exec, на уже запущеном контейнере вызываю bash, через ls показываю примапленный volume.

```
[step4j@step4j-vm]$ docker run -d -v /data:/data centos sleep infinity
867d3a86aa2a60f3806d1d5d25c0957676cd8942d1fea34263584466491eb670

[step4j@step4j-vm]$ docker ps
CONTAINER ID   IMAGE     COMMAND            CREATED         STATUS         PORTS     NAMES
867d3a86aa2a   centos    "sleep infinity"   4 seconds ago   Up 4 seconds             bold_tu

[step4j@step4j-vm]$ docker exec -it 867d3a86aa2a bash
[root@867d3a86aa2a /]#

[root@867d3a86aa2a /]# ls -lah /
total 60K
drwxr-xr-x   1 root root 4.0K Jan 24 20:21 .
drwxr-xr-x   1 root root 4.0K Jan 24 20:21 ..
-rwxr-xr-x   1 root root    0 Jan 24 20:21 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   2 root root 4.0K Jan 24 19:12 data
drwxr-xr-x   5 root root  340 Jan 24 20:21 dev
drwxr-xr-x   1 root root 4.0K Jan 24 20:21 etc
drwxr-xr-x   2 root root 4.0K Nov  3  2020 home
lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root 4.0K Sep 15 14:17 lost+found
drwxr-xr-x   2 root root 4.0K Nov  3  2020 media
drwxr-xr-x   2 root root 4.0K Nov  3  2020 mnt
drwxr-xr-x   2 root root 4.0K Nov  3  2020 opt
dr-xr-xr-x 363 root root    0 Jan 24 20:21 proc
dr-xr-x---   2 root root 4.0K Sep 15 14:17 root
drwxr-xr-x  11 root root 4.0K Sep 15 14:17 run
lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root 4.0K Nov  3  2020 srv
dr-xr-xr-x  13 root root    0 Jan 24 20:21 sys
drwxrwxrwt   7 root root 4.0K Sep 15 14:17 tmp
drwxr-xr-x  12 root root 4.0K Sep 15 14:17 usr
drwxr-xr-x  20 root root 4.0K Sep 15 14:17 var
```

```
[step4j@step4j-vm]$ docker run -v /data:/data -d debian sleep infinity
4561ae7cd80e4a6b87c042a9d4d020a37ab712662aae697af45404bf9a61bda8

[step4j@step4j-vm]$ docker ps
CONTAINER ID   IMAGE     COMMAND            CREATED         STATUS         PORTS     NAMES
4561ae7cd80e   debian    "sleep infinity"   5 seconds ago   Up 4 seconds             friendly_maxwell
867d3a86aa2a   centos    "sleep infinity"   3 minutes ago   Up 3 minutes             bold_tu
 
root@4561ae7cd80e:/# ls -lah /
total 76K
drwxr-xr-x   1 root root 4.0K Jan 24 20:25 .
drwxr-xr-x   1 root root 4.0K Jan 24 20:25 ..
-rwxr-xr-x   1 root root    0 Jan 24 20:25 .dockerenv
drwxr-xr-x   2 root root 4.0K Dec 20 00:00 bin
drwxr-xr-x   2 root root 4.0K Dec 11 17:25 boot
drwxr-xr-x   2 root root 4.0K Jan 24 19:12 data
drwxr-xr-x   5 root root  340 Jan 24 20:25 dev
drwxr-xr-x   1 root root 4.0K Jan 24 20:25 etc
drwxr-xr-x   2 root root 4.0K Dec 11 17:25 home
drwxr-xr-x   8 root root 4.0K Dec 20 00:00 lib
drwxr-xr-x   2 root root 4.0K Dec 20 00:00 lib64
drwxr-xr-x   2 root root 4.0K Dec 20 00:00 media
drwxr-xr-x   2 root root 4.0K Dec 20 00:00 mnt
drwxr-xr-x   2 root root 4.0K Dec 20 00:00 opt
dr-xr-xr-x 307 root root    0 Jan 24 20:25 proc
drwx------   2 root root 4.0K Dec 20 00:00 root
drwxr-xr-x   3 root root 4.0K Dec 20 00:00 run
drwxr-xr-x   2 root root 4.0K Dec 20 00:00 sbin
drwxr-xr-x   2 root root 4.0K Dec 20 00:00 srv
dr-xr-xr-x  13 root root    0 Jan 24 20:25 sys
drwxrwxrwt   2 root root 4.0K Dec 20 00:00 tmp
drwxr-xr-x  11 root root 4.0K Dec 20 00:00 usr
drwxr-xr-x  11 root root 4.0K Dec 20 00:00 var

```
С контейнера под centos создаю в директории файл

```
[root@867d3a86aa2a /]# echo '' > /data/centos-file-1
[root@867d3a86aa2a /]# ls /data
centos-file-1

```

Создаю файл с хоста

```
[step4j@step4j-vm]$ echo '' > /data/fedora-host-file-2
[step4j@step4j-vm]$ ls /data
centos-file-1  fedora-host-file-2

```
Листинг файлов в директории из контейнера debian

```
root@4561ae7cd80e:/# ls -lah /data
total 16K
drwxr-xrwx 2 root root 4.0K Jan 24 20:38 .
drwxr-xr-x 1 root root 4.0K Jan 24 20:25 ..
-rw-r--r-- 1 root root    1 Jan 24 20:29 centos-file-1
-rw-rw-r-- 1 1000 1000    1 Jan 24 20:38 fedora-host-file-2
```
