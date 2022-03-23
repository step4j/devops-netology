# Домашнее задание "3.9. Элементы безопасности информационных систем"


***1.Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.***

Ответ - Готово.

***2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.***

Ответ - ![bitwarden](https://user-images.githubusercontent.com/95530808/159172436-4362a3bf-c98a-4b6d-8ae0-0c22ff33ccf0.PNG)

***3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.***

Ответ - 

```
sudo apt get install apache2

cd /etc/apache2

sudo mkdir ssl ; cd ssl

sudo openssl req -new -x509 -days 1461 -nodes -out cert.pem -keyout cert.key -subj "/C=RU/ST=SPb/L=SPb/O=Global Security/OU=IT Department/CN=test.dmosk.local/CN=test"

a2enmod ssl

apachectl -M | grep ssl

ssl_module (shared)

systemctl restart httpd

systemctl restart apache2

service apache2 restart

cd ..

sudo nano sites-enabled/site

В открытый файл добавил следующее:

<VirtualHost *:443>
    ServerName site.ru
    DocumentRoot /var/www/apache/data
    SSLEngine on
    SSLCertificateFile ssl/cert.pem
    SSLCertificateKeyFile ssl/cert.key
    #SSLCertificateChainFile ssl/cert.ca-bundle
</VirtualHost>

apachectl configtest

Syntax OK

apachectl graceful

```

![apache](https://user-images.githubusercontent.com/95530808/159172789-36611d7b-391f-4119-8b1e-d99803ff9876.PNG)

***4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).***

Ответ - 

```
vagrant@vagrant:~$ git clone --depth 1 https://github.com/drwetter/testssl.sh.git
Cloning into 'testssl.sh'...
remote: Enumerating objects: 100, done.
remote: Counting objects: 100% (100/100), done.
remote: Compressing objects: 100% (93/93), done.
remote: Total 100 (delta 14), reused 25 (delta 6), pack-reused 0
Receiving objects: 100% (100/100), 8.61 MiB | 1.41 MiB/s, done.
Resolving deltas: 100% (14/14), done.
vagrant@vagrant:~$ cd testssl.sh

```

![test mail](https://user-images.githubusercontent.com/95530808/159173014-0a44dd3e-b0b7-4fa8-9056-d4b22c68f949.PNG)

***5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.***

Ответ - 

```
apt install openssh-serversystemctl 
start sshd.servicesystemctl 
enable sshd.service

vagrant@vagrant:/home$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/vagrant/.ssh/id_rsa
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:/cDMYCw1Gz2c6261Qp+Z3icpCg51tUJPortxeb3a7qY vagrant@vagrant
The key's randomart image is:
+---[RSA 3072]----+
|        +o .     |
|       o +=      |
|      . = ooo    |
|       o O.= .   |
|        S.O o    |
|       . oo=..   |
|      . +oooo=.. |
|       o =+.Bo+..|
|        o..+EB*o |
+----[SHA256]-----+

```

```

cat ~/.ssh/id_rsa.pub

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCqjclPoETEV+EYtkwT9HCXHJCkn+KtVR3HBDem2T7/iV/U0SZ/dJrbKRyHqyDYqG+5ohf6+ScF2fIvg27m3fNJChXoF1rk5ZHynN9bqzu0xJKrak7eZ6Jqc331ZCf3foibugXeGtM+oYbGIXbpDPxrNpcVfeweGkR9i3FSSixg9h7X1IKnNbWYac/izNkfQBWHWOMiK8fdD8i1LbI48At/F5DBGTEiamhL7hqxESdKCQK55k065YgTEV2rxLQV5sn6+MuD7kOrb9yu4qXCWMh/KLIWYohr2CYmWe+UhdyPp6wePa61+Fxl+k1FK3CIENPEejDnFYw+SE3YVJrxXHX60IqzxlUupzs0+w6fFSy+rZOC2dSZ8EbKEWvZBeJCXORi5qmTyG7ByANcyOggqHNlRc/AeLp7rNXonSjuC4glW5U6QLRGftZAkOQz496oTB20Uf3AVKd1GKJ6RX8lF2XozIld7xu2mFIIjgYJatFJzTu0y98mj2vLDatiYO+I3DU= vagrant@vagrant

на другом сервере - 

cd .ssh

sudo nano authorized_keys 

Вставляем ключ из вывода id_rsa.pub

ssh vagrant@192.168.0.108

```

***6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.***

Ответ - 
```
mv id_rsa.pub mykey.pub

mv id_rsa  mykey

ll

total 24
drwx------ 2 vagrant root    4096 Mar 23 14:56 ./
drwxr-xr-x 7 vagrant vagrant 4096 Mar 22 17:52 ../
-rw------- 1 vagrant vagrant  389 Mar 19 10:14 authorized_keys
-rw-r--r-- 1 vagrant vagrant  444 Mar 22 16:18 known_hosts
-rw------- 1 vagrant vagrant 2602 Mar 20 16:47 mykey.pub
-rw-r--r-- 1 vagrant vagrant  569 Mar 20 16:47 mykey
touch ~/.ssh/config

nano ~/.ssh/config

Host vagrant
  Hostname 192.168.0.116
  User test

ssh vagrant

test@192.168.0.116's password:

```

***7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.***

Ответ - 
```

sudo tcpdump -c 100 -i enp0s3 -w 0001.pcap

tcpdump: listening on enp0s3, link-type EN10MB (Ethernet), capture size 262144 bytes

100 packets captured

102 packets received by filter

0 packets dropped by kernel

sudo apt install wireshark

```

![wireshark](https://user-images.githubusercontent.com/95530808/159725741-1a3f10d0-7d4c-4ab2-8ea1-6f4dda681f59.PNG)
