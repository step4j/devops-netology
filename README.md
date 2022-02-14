# Домашнее задание "3.4. Операционные системы,лекция 2"

***1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:***
***поместите его в автозагрузку,
предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.***

Ответ - установил node_exporter 

Создал юнит файл по пути ```/etc/systemd/system/node_exporter.service```

Прописал в нём следующий код 

```[Unit]```

```Description=Prometheus Node Exporter```

```Wants=network-online.target```

```After=network-online.target```

```[Service]```

```User=node_exporter```

```Group=node_exporter```

```Type=simple```

```ExecStart=/usr/local/bin/node_exporter```

```[Install]```

```WantedBy=multi-user.target```

Остановил и запустил node_exporter и проверил статус - всё работает.

```systemctl status node_exporter```

```node_exporter.service - Prometheus Node Exporter```
     
```Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)```
     
```Active: active (running) since Sun 2022-02-06 14:37:09 UTC; 1 day 19h ago```

```Main PID: 641 (node_exporter)```

***2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.***

Ответ - 

CPU - 
```node_cpu_seconds_total{cpu="0",mode="system"} 183.18```

```node_cpu_seconds_total{cpu="0",mode="user"} 92.4```

DISK - 

```node_disk_io_time_seconds_total{device="sda"}```

```node_disk_read_bytes_total{device="sda"}```

MEM - 

```node_memory_MemFree_bytes gauge```

```node_memory_MemTotal_bytes gauge```

NETWORK 

```node_network_receive_errs_total{device="eth0"}```

```node_network_receive_bytes_total{device="eth0"}```

***3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки ```(sudo apt install -y netdata)```. После успешной установки:
в конфигурационном файле ```/etc/netdata/netdata.conf``` в секции ```[web]``` замените значение с localhost на ```bind to = 0.0.0.0,```
добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте ```vagrant reload:```
```config.vm.network "forwarded_port", guest: 19999, host: 19999```
После успешной перезагрузки в браузере на своем ПК (не в виртуальной машине) вы должны суметь зайти на ```localhost:19999```. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.***

Ответ - Установил Netdata, всё запускается через браузер на родном ПК, с метриками ознакомился.
![netdata](https://user-images.githubusercontent.com/95530808/152971830-50a5b16a-b738-4c17-bbef-56a86871a2fc.PNG)

***4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?***

Можно. Пример - ```[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006```
      
      
***5. Как настроен ```sysctl fs.nr_open``` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (```ulimit --help```)?

Ответ - fs.nr_open - это Максимальное количество файловых дескрипторов, поддерживаемых ядром, то есть максимальное количество файловых дескрипторов, используемых процессом.

Вывод ```sysctl fs.nr_open``` - ```fs.nr_open = 1048576```

Лимит который не позволит достичь такого числа

```vagrant@vagrant:~$ ulimit -Hn```

```1048576```

жесткий лимит на пользователя (не может быть увеличен, только уменьшен

***6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.***

```root@vagrant:~# ps -e |grep sleep```

```2020 pts/2    00:00:00 sleep```

```root@vagrant:~# nsenter --target 2020 --pid --mount```

```root@vagrant:/# ps```

```PID TTY          TIME CMD```

```2 pts/0    00:00:00 bash```

```11 pts/0    00:00:00 ps```

***7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?***

Ответ - судя по выводу ```dmesq``` помог этот механизм ```[  513.417816] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-3.scope```

Из того что нашёл в интернете про эту команду

Это называется fork bomb.

```:()``` означает, что вы определяете функцию под названием :

```{:|: &}```означает запустить функцию :и :снова отправить ее вывод в функцию и запустить ее в фоновом режиме.

Это ```;```разделитель команд.

```:``` запускает функцию в первый раз.

По сути, вы создаете функцию, которая вызывает себя дважды при каждом вызове и не имеет возможности завершить себя. Он будет удваиваться, пока у вас не закончатся системные ресурсы.

