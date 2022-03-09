# Домашнее задание "3.8. Компьютерные сети, лекция 3

***1.Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP***

Ответ - 
```
route-views>show ip route 5.44.173.47
Routing entry for 5.44.172.0/23
  Known via "bgp 6447", distance 20, metric 0
  Tag 6939, type external
  Last update from 64.71.137.241 3w6d ago
  Routing Descriptor Blocks:
  * 64.71.137.241, from 64.71.137.241, 3w6d ago
      Route metric is 0, traffic share count is 1
      AS Hops 3
      Route tag 6939
      MPLS label: none
```
```
route-views>show bgp 5.44.173.47
BGP routing table entry for 5.44.172.0/23, version 301716243
Paths: (23 available, best #17, table default)
  Not advertised to any peer
  Refresh Epoch 1
  3333 31133 40995
    193.0.0.56 from 193.0.0.56 (193.0.0.56)
      Origin IGP, localpref 100, valid, external
      path 7FE03E0EC7B8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  101 174 31133 40995
    209.124.176.223 from 209.124.176.223 (209.124.176.223)
      Origin IGP, localpref 100, valid, external
      Community: 101:20100 101:20110 101:22100 174:21101 174:22005
      Extended Community: RT:101:22100
      path 7FE0A82F9D48 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 2
  8283 31133 40995
    94.142.247.3 from 94.142.247.3 (94.142.247.3)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 8283:1 8283:101
      unknown transitive attribute: flag 0xE0 type 0x20 length 0x18
        value 0000 205B 0000 0000 0000 0001 0000 205B
              0000 0005 0000 0001
      path 7FE03539EBA8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  1351 6939 31133 40995
 --More--
```

***2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.***

Ответ - 

```
root@vagrant:~# ip link add dummy0 type dummy
root@vagrant:~# ip addr add 10.0.29.0/24 dev dummy0
root@vagrant:~# ip link set dummy0 up
root@vagrant:~# ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 85238sec preferred_lft 85238sec
    inet6 fe80::a00:27ff:feb1:285d/64 scope link
       valid_lft forever preferred_lft forever
3: dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether ae:bc:d8:a9:3c:61 brd ff:ff:ff:ff:ff:ff
    inet 10.0.29.0/24 scope global dummy0
       valid_lft forever preferred_lft forever
    inet6 fe80::acbc:d8ff:fea9:3c61/64 scope link
       valid_lft forever preferred_lft forever
 ```
 
 ```
 root@vagrant:~# ip route add 8.8.8.0/24 via 10.0.2.1
root@vagrant:~# ip route add 8.16.28.0/24 via 10.0.29.0
root@vagrant:~# ip route
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100
8.8.8.0/24 via 10.0.2.1 dev eth0
8.16.28.0/24 via 10.0.29.0 dev dummy0
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100
10.0.29.0/24 dev dummy0 proto kernel scope link src 10.0.29.0

```

***3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.***

Ответ - 

```
root@vagrant:~# ss -tpan
State   Recv-Q  Send-Q   Local Address:Port   Peer Address:Port  Process
LISTEN  0       4096     127.0.0.53%lo:53          0.0.0.0:*      users:(("systemd-resolve",pid=617,fd=13))
LISTEN  0       128            0.0.0.0:22          0.0.0.0:*      users:(("sshd",pid=680,fd=3))
ESTAB   0       0            10.0.2.15:22         10.0.2.2:52106  users:(("sshd",pid=1349,fd=4),("sshd",pid=1302,fd=4))
LISTEN  0       128               [::]:22             [::]:*      users:(("sshd",pid=680,fd=4))

```

22 - TCP,UDP - под SSH соединение, использует его sshd - is the OpenSSH server process 

53 - TCP,UDP - под DNS, systemd-resolve

***4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?***

Ответ - 
```
root@vagrant:~# ss -upan
State    Recv-Q   Send-Q      Local Address:Port       Peer Address:Port   Process
UNCONN   0        0           127.0.0.53%lo:53              0.0.0.0:*       users:(("systemd-resolve",pid=617,fd=12))
UNCONN   0        0          10.0.2.15%eth0:68              0.0.0.0:*       users:(("systemd-network",pid=615,fd=19))

```

***5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.***

Ответ - https://drive.google.com/file/d/1Q9nqM7C7NDn0eqpQsT3pteJ4voDMEEYZ/view?usp=sharing
