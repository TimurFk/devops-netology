# devops-netology
Здравствуйте, Фкирдымов Тимур комментарий к заданию 3.9

***1.	Установите Bitwarden плагин для браузера. Зарегистрируйтесь и сохраните несколько паролей.***  
https://disk.yandex.ru/i/ufedPqnSIoXMkQ 

***2.	Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden аккаунт через Google authenticator OTP.***  
https://disk.yandex.ru/i/HetCYOSMKdCvTg

***3.	Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.***  
$ sudo apt update  
$ sudo apt install apache2  
$ sudo a2enmod ssl  
$ sudo systemctl restart apache2  
$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt  
$ sudo nano /etc/apache2/sites-available/192.168.39.35.conf  
<VirtualHost *:443>  
   ServerName 192.168.39.35  
   DocumentRoot /var/www/192.168.39.35

   SSLEngine on  
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt  
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key  
</VirtualHost>  
$ sudo mkdir /var/www/192.168.39.35  
$ sudo nano /var/www/192.168.39.35/index.html  
#<h1>Rabotaet!!!</h1>  
$ sudo a2ensite 192.168.39.35.conf  
$ sudo systemctl reload apache2  
$ sudo apache2ctl configtest  
Syntax OK  
Проверяю в браузере https://192.168.39.35  
https://disk.yandex.ru/i/_Mj3sMnHgYfomQ  
https://disk.yandex.ru/i/-waGOqkuyqiF_A  

***4.	 Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).***  
$ git clone --depth 1 https://github.com/drwetter/testssl.sh.git  
$ cd testssl.sh  
~/testssl.sh$ ./testssl.sh -p --parallel --sneaky https://www.xxpm.ru/

Проверял рабочий сайт, поэтому частично замазал  
https://disk.yandex.ru/i/ICj0hHqb5ub9WA 

***5.	Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.***  
$ hostname  
vagrant  
$ sudo apt install openssh-server  
$ sudo systemctl start sshd.service  
$ ssh-keygen  
#Копирую ключ на вторую машину  
$ ssh-copy-id vagrant@192.168.39.34                     		
Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@192.168.39.34'"  
#Подключаюсь ко второй машине  
$ ssh vagrant@192.168.39.34  					
Подключение прошло успешно - https://disk.yandex.ru/i/frwizDi6aLlaLQ 

***6.	Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.***  
~$ cd /home/vagrant/.ssh  
~/.ssh$ ls  
authorized_keys  id_rsa  id_rsa.pub  known_hosts  
~/.ssh$ mv id_rsa second_rsa  
~/.ssh$ mv id_rsa.pub second_rsa.pub  
~/.ssh$ nano config  
Host vagrant2  
User vagrant  
Hostname 192.168.39.34  
IdentityFile ~/.ssh/second_rsa  
$ ssh vagrant@vagrant2  
Подключение прошло успешно - https://disk.yandex.ru/i/dCKK9r_kN3BQbg

***7.	Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.***  
$ tcpdump -i eth0 -c 100 -w 1601.pcap  
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes  
100 packets captured  
102 packets received by filter  
0 packets dropped by kernelpcap

Открыл в Wireshark - https://disk.yandex.ru/d/whfWfIFCl_tcKg 
