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
