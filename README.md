# Домашнее задание "3.5. Файловые системы 

***1. Узнайте о sparse (разряженных) файлах.***

Ответ - Разрежённый файл (англ. sparse file) — файл, в котором последовательности нулевых байтов[1] заменены на информацию об этих последовательностях (список дыр).

Дыра (англ. hole) — последовательность нулевых байт внутри файла, не записанная на диск. Информация о дырах (смещение от начала файла в байтах и количество байт) хранится в метаданных ФС.

***2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?***

Ответ - Жесткая ссылка и файл, для которой она создавалась имеют одинаковые inode. Поэтому жесткая ссылка имеет те же права доступа, владельца и время последней модификации, что и целевой файл. Различаются только имена файлов. Фактически жесткая ссылка это еще одно имя для файла

***3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:

Ответ - Готово, сделал vagrant destroy, файлы заменил.

***4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.***

Ответ - цифры немного отличились от задачи

```Device     Boot Start     End Sectors  Size Id Type```

```/dev/sdb1       50000 3956735 3906736  1.9G 83 Linux```

```/dev/sdb2        2048  978943  976896  477M 83 Linux```

***5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.***

Ответ - 

```root@vagrant:~# sfdisk -d /dev/sdb|sfdisk --force /dev/sdc```

```Checking that no-one is using this disk right now ... OK```

```Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors```

```Disk model: VBOX HARDDISK```

```Units: sectors of 1 * 512 = 512 bytes```

```Sector size (logical/physical): 512 bytes / 512 bytes```

```I/O size (minimum/optimal): 512 bytes / 512 bytes```

```>>> Script header accepted.```

```>>> Script header accepted.```

```>>> Script header accepted.```

```>>> Script header accepted.```

```>>> Created a new DOS disklabel with disk identifier 0xf3d17d08.```

```/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 1.9 GiB.```

```/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 477 MiB.```

```/dev/sdc3: Done.```

```New situation:```

```Disklabel type: dos```

```Disk identifier: 0xf3d17d08```

```Device     Boot Start     End Sectors  Size Id Type```

```/dev/sdc1       50000 3956735 3906736  1.9G 83 Linux```

```/dev/sdc2        2048  978943  976896  477M 83 Linux```

```Partition table entries are not in disk order.```

```The partition table has been altered.```

```Calling ioctl() to re-read partition table.```

```Syncing disks.```

Результат - 

```Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors```

```Disk model: VBOX HARDDISK```

```Units: sectors of 1 * 512 = 512 bytes```

```Sector size (logical/physical): 512 bytes / 512 bytes```

```I/O size (minimum/optimal): 512 bytes / 512 bytes```

```Disklabel type: dos```

```Disk identifier: 0xf3d17d08```

```Device     Boot Start     End Sectors  Size Id Type```

```/dev/sdb1       50000 3956735 3906736  1.9G 83 Linux```

```/dev/sdb2        2048  978943  976896  477M 83 Linux```

```Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors```

```Disk model: VBOX HARDDISK```

```Units: sectors of 1 * 512 = 512 bytes```

```Sector size (logical/physical): 512 bytes / 512 bytes```

```I/O size (minimum/optimal): 512 bytes / 512 bytes```

```Disklabel type: dos```

```Disk identifier: 0xf3d17d08```

```Device     Boot Start     End Sectors  Size Id Type```

```/dev/sdc1       50000 3956735 3906736  1.9G 83 Linux```

```/dev/sdc2        2048  978943  976896  477M 83 Linux```

***6. Соберите mdadm RAID1 на паре разделов 2 Гб.***

Ответ - 

```root@vagrant:~# mdadm --create --verbose /dev/md1 -l 1 -n 2 /dev/sd{b1,c1}```

```mdadm: Note: this array has metadata at the start and```
    
```may not be suitable as a boot device.  If you plan to```
    
```store '/boot' on this device please ensure that```

```your boot-loader understands md/v1.x metadata, or use```

```--metadata=0.90```

```mdadm: size set to 1951296K```

```Continue creating array? y```

```mdadm: Defaulting to version 1.2 metadata```

```mdadm: array /dev/md1 started.```

***7. Соберите mdadm RAID0 на второй паре маленьких разделов.***

Ответ - 

```root@vagrant:~# mdadm --create --verbose /dev/md0 -l 1 -n 2 /dev/sd{b2,c2}```

```mdadm: Note: this array has metadata at the start and```

```may not be suitable as a boot device.  If you plan to```

```store '/boot' on this device please ensure that```

```your boot-loader understands md/v1.x metadata, or use```

```--metadata=0.90```

```mdadm: size set to 487424K```

```Continue creating array? y```

```mdadm: Defaulting to version 1.2 metadata```

```mdadm: array /dev/md0 started.```

***8. Создайте 2 независимых PV на получившихся md-устройствах.***

Ответ - 
```root@vagrant:~# pvcreate /dev/md1 /dev/md0```
  
```Physical volume "/dev/md1" successfully created.```

```Physical volume "/dev/md0" successfully created.```

***9. Создайте общую volume-group на этих двух PV.***

Ответ - ```root@vagrant:~# vgcreate vg1 /dev/md1 /dev/md0```
  ```Volume group "vg1" successfully created```
  
 ***10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.***
 
 Ответ - ```root@vagrant:~# lvcreate -L 100M vg1 /dev/md0```
  ```Logical volume "lvol0" created.```
  
***11. Создайте mkfs.ext4 ФС на получившемся LV.***

Ответ - ```root@vagrant:~# mkfs.ext4 /dev/vg1/lvol0```

```mke2fs 1.45.5 (07-Jan-2020)```

```Creating filesystem with 25600 4k blocks and 25600 inodes```

```Allocating group tables: done```

```Writing inode tables: done```

```Creating journal (1024 blocks): done```

```Writing superblocks and filesystem accounting information: done```

***12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.***

Ответ - ```root@vagrant:~# mkdir /tmp/new```

```root@vagrant:~# mount /dev/vg1/lvol0  /tmp/new```

***13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.***

Ответ - ```wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.```

```--2022-02-20 09:15:22--  https://mirror.yandex.ru/ubuntu/ls-lR.gz```

```Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183```

```Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.```

```HTTP request sent, awaiting response... 200 OK```

```Length: 22312478 (21M) [application/octet-stream]```

```Saving to: ‘/tmp/new/test.gz.’```

```/tmp/new/test.gz.             100%[=================================================>]  21.28M  3.24MB/s    in 6.9s```

```2022-02-20 09:15:30 (3.09 MB/s) - ‘/tmp/new/test.gz.’ saved [22312478/22312478]```

```root@vagrant:~# ls -l /tmp/new```

```total 21808```

```drwx------ 2 root root    16384 Feb 20 09:11 lost+found```

```-rw-r--r-- 1 root root 22312478 Feb 20 06:42 test.gz.```

***14. Прикрепите вывод lsblk.***

Ответ - 

```NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT```

```loop0                       7:0    0 70.3M  1 loop  /snap/lxd/21029```

```loop2                       7:2    0 55.4M  1 loop  /snap/core18/2128```

```loop3                       7:3    0 43.6M  1 loop  /snap/snapd/14978```

```loop4                       7:4    0 61.9M  1 loop  /snap/core20/1328```

```loop5                       7:5    0 55.5M  1 loop  /snap/core18/2284```

```loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835```

```sda                         8:0    0   64G  0 disk```

```├─sda1                      8:1    0    1M  0 part```

```├─sda2                      8:2    0    1G  0 part  /boot```

```└─sda3                      8:3    0   63G  0 part```

```└─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /```

```sdb                         8:16   0  2.5G  0 disk```

```├─sdb1                      8:17   0  1.9G  0 part```

```│ └─md1                     9:1    0  1.9G  0 raid1```

```└─sdb2                      8:18   0  477M  0 part```

```└─md0                     9:0    0  476M  0 raid1```

```└─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new```

```sdc                         8:32   0  2.5G  0 disk```

```├─sdc1                      8:33   0  1.9G  0 part```

```│ └─md1                     9:1    0  1.9G  0 raid1```

```└─sdc2                      8:34   0  477M  0 part```

```└─md0                     9:0    0  476M  0 raid1```

```└─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new```


***15. Протестируйте целостность файла:***

Ответ - 

```root@vagrant:~# apt install gzip```

```root@vagrant:~# gzip -t /tmp/new/test.gz.```

```root@vagrant:~# echo $?```
```0```

***16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.***

Ответ - 

```root@vagrant:~# pvmove /dev/md0```
  
  ```/dev/md0: Moved: 12.00%```
  
  ```/dev/md0: Moved: 100.00%```

```root@vagrant:~# lsblk```

```NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT```

```sda                    8:0    0   64G  0 disk```

```├─sda1                 8:1    0  512M  0 part  /boot/efi```

```├─sda2                 8:2    0    1K  0 part```

```└─sda5                 8:5    0 63.5G  0 part```  

```├─vgvagrant-root   253:0    0 62.6G  0 lvm   /```

```└─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]```

```sdb                    8:16   0  2.5G  0 disk```  

```├─sdb1                 8:17   0    2G  0 part```  

```│ └─md1                9:1    0    2G  0 raid1``` 

```│   └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new```

```└─sdb2                 8:18   0  511M  0 part```  

```└─md0                9:0    0  510M  0 raid1``` 

```sdc                    8:32   0  2.5G  0 disk```  

```├─sdc1                 8:33   0    2G  0 part```  

```│ └─md1                9:1    0    2G  0 raid1``` 

```│   └─vg1-lvol0      253:2    0  100M  0 lvm   /tmp/new```

```└─sdc2                 8:34   0  511M  0 part```  

```└─md0                9:0    0  510M  0 raid1``` 

 ***17. Сделайте --fail на устройство в вашем RAID1 md.***
 
 Ответ - 
```root@vagrant:~# mdadm /dev/md1 --fail /dev/sdb1```

```mdadm: set /dev/sdb1 faulty in /dev/md1```

```root@vagrant:~# mdadm -D /dev/md1```

```/dev/md1:```
           
           ```Version : 1.2```

```*******```

```Consistency Policy : resync```

              
              ```Name : vagrant:1  (local to host vagrant)```
              
              ```UUID : 1aac0a29:```

```root@vagrant:~# mdadm -D /dev/md1```

```/dev/md1:```
           
```Version : 1.2```

```0fa0dc73:082e1042:fe00b376```
            
```Events : 19```

```Number   Major   Minor   RaidDevice State```
       
``` -       0        0        0      removed```
       
``` 1       8       33        1      active sync   /dev/sdc1```

``` 0       8       17        -      faulty   /dev/sdb1```

***18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.***

Ответ - 
```root@vagrant:~# dmesg |grep md1```

```[  480.422928] md/raid1:md1: not clean -- starting background reconstruction```

```[  480.422930] md/raid1:md1: active with 2 out of 2 mirrors```

```[  480.422945] md1: detected capacity change from 0 to 2144337920```

```[  480.425781] md: resync of RAID array md1```

```[  490.758344] md: md1: resync done.```

```[ 2325.890719] md/raid1:md1: Disk failure on sdb1, disabling device.```
 
```md/raid1:md1: Operation continuing on 1 device```

***19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:***

Ответ - ```root@vagrant:~# gzip -t /tmp/new/test.gz```

```root@vagrant:~# echo $?```

```0```

***20. Погасите тестовый хост, vagrant destroy.***

```22:12:43 alex@upc(0):~/vagrant$ vagrant destroy```
    
    ```default: Are you sure you want to destroy the 'default' VM? [y/N] y```

```==> default: Forcing shutdown of VM...```

```==> default: Destroying VM and associated drives...```
      
