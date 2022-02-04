# Домашнее задание "3.3. Операционные системы,лекция 1"

***1. Какой системный вызов делает команда cd? В прошлом ДЗ мы выяснили, что cd не является самостоятельной программой, это shell builtin, поэтому запустить strace непосредственно на cd не получится. Тем не менее, вы можете запустить strace на /bin/bash -c 'cd /tmp'. В этом случае вы увидите полный список системных вызовов, которые делает сам bash при старте. Вам нужно найти тот единственный, который относится именно к cd.***

Ответ - ```chdir("/tmp")```

***2. Попробуйте использовать команду file на объекты разных типов на файловой системе. Например:***


```vagrant@netology1:~$ file /dev/tty```

```/dev/tty: character special (5/0)```

```vagrant@netology1:~$ file /dev/sda```

```/dev/sda: block special (8/0)```

```vagrant@netology1:~$ file /bin/bash```

```/bin/bash: ELF 64-bit LSB shared object, x86-64```

Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.

Ответ - ```openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3```

***3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).***

Ответ - Делаем ping в файл ping_log, удаляем ping_log, убеждаемся что он (deleted) но размер растет:

```vagrant@vagrant:~ ping ya.ru > ping_log & rm ping_log```

```[1] 8349```

```vagrant@vagrant:~ ps```

```PID TTY          TIME CMD```

```7599 pts/1    00:00:00 bash```

```8349 pts/1    00:00:00 ping```

```8351 pts/1    00:00:00 ps```

```vagrant@vagrant:~ sudo lsof -p 8349```

```COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME```

```ping    8349 vagrant    1w   REG  253,0     2141 131094 /home/vagrant/ping_log (deleted)```

```vagrant@vagrant:~ sudo lsof -p 8349```

```COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME```

```ping    8349 vagrant    1w   REG  253,0     3401 131094 /home/vagrant/ping_log (deleted)```

Ограничим размер файла ping_log 10MB:

```vagrant@vagrant:~ sudo truncate -s 10MB /proc/8349/fd/1```

Или можно очистить совсем: ```vagrant@vagrant:~ sudo truncate -s 0 /proc/8349/fd/1```

***4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?***

Ответ - "Зомби" процессы, в отличии от "сирот" освобождают свои ресурсы, но не освобождают запись в таблице процессов. 
запись освободиться при вызове wait() родительским процессом.

***5. В iovisor BCC есть утилита ```opensnoop```:
```root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop```
```/usr/sbin/opensnoop-bpfcc```
На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты? Воспользуйтесь пакетом bpfcc-tools для Ubuntu 20.04. Дополнительные сведения по установке.***

Ответ - ```root@vagrant:~# /usr/sbin/opensnoop-bpfcc```

```PID    COMM               FD ERR PATH```
```766    vminfo              6   0 /var/run/utmp```
```562    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services```
```562    dbus-daemon        18   0 /usr/share/dbus-1/system-services```
```562    dbus-daemon        -1   2 /lib/dbus-1/system-services```
```562    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/```

***6. Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.***

Ответ - системный вызов uname()

Цитата :
     ```Part of the utsname information is also accessible  via  /proc/sys/ker‐
       nel/{ostype, hostname, osrelease, version, domainname}.```
       
 ***7. Чем отличается последовательность команд через ; и через && в bash? Например:
root@netology1:~ test -d /tmp/some_dir; echo Hi
Hi
root@netology1:~ test -d /tmp/some_dir && echo Hi
root@netology1:~
Есть ли смысл использовать в bash &&, если применить set -e?***

Ответ - ```;``` - Команды будут выполняться одна за другой независимо успешно выполнилась прошлая или нет.

```&&``` - При успешном выполнении первой команды запуститься вторая.

```set-e``` - это немедленный выход, если команда завершается с ненулевым статусом, поэтому вместе c ```&&``` не вижу смысла её использовать.

***8. Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?

Ответ - e - немедленный выход, если команда завершается с ненулевым статусом

u - Считать неустановленные переменные ошибкой при подстановке. ( Не совсем понял эту команду, в моём понимании если использовать -u то если в каком либо коде присутствует лишний аргумент то он выдаст ошибку? Если не так объясните пожалуйста)

x - Печатать команды и их аргументы по мере их выполнения.

o - Имя опции

Исходя из перевода euxo - думаю что хорошо его использовать для прерывания неккоректных программ и смотреть на какой команде произошёл сбой. ( Мог не правильно это понять, если что поправьте меня пожалуйста)

***9. Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе. В man ps ознакомьтесь (/PROCESS STATE CODES) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).***

Ответ - ``PROCESS STATE CODES
       Here are the different values that the s, stat and state output specifiers (header "STAT" or "S") will display to describe the
       state of a process:``

               ```D    uninterruptible sleep (usually IO)
               I    Idle kernel thread
               R    running or runnable (on run queue)
               S    interruptible sleep (waiting for an event to complete)
               T    stopped by job control signal
               t    stopped by debugger during the tracing
               W    paging (not valid since the 2.6.xx kernel)
               X    dead (should never be seen)
               Z    defunct ("zombie") process, terminated but not reaped by its parent```
