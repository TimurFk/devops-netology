# devops-netology

Здравствуйте, Фкирдымов Тимур комментарий к заданию 3.8


***1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP***  
***telnet route-views.routeviews.org***  
***Username: rviews***  
***show ip route x.x.x.x/32***  
***show bgp x.x.x.x/32***

Routing entry for 5.39.160.0/21  
Known via "bgp 6447", distance 20, metric 0  
Tag 6939, type external  
Last update from 64.71.137.241 23:10:58 ago  
Routing Descriptor Blocks:  
* 64.71.137.241, from 64.71.137.241, 23:10:58 ago  
Route metric is 0, traffic share count is 1  
AS Hops 3  
Route tag 6939  
MPLS label: none

BGP routing table entry for 5.39.160.0/21, version 1838928703  
Paths: (23 available, best #22, table default)  
…  
Refresh Epoch 1  
6939 8331 58157  
64.71.137.241 from 64.71.137.241 (216.218.252.164)  
Origin IGP, localpref 100, valid, external, best  
unknown transitive attribute: flag 0xE0 type 0x20 length 0xC  
value 0000 21B7 0000 0777 0000 21B7  
path 7FE18BAD1DE8 RPKI State not found  
rx pathid: 0, tx pathid: 0x0
	

***2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.***

~$ sudo -i  
~# echo "dummy" >> /etc/modules  
~# echo "options dummy numdummies=1" > /etc/modprobe.d/dummy.conf  
~# nano /etc/network/interfaces  
source-directory /etc/network/interfaces.d  
auto dummy0  
iface dummy0 inet static  
address 192.168.18.2/24  
pre-up ip link add dummy0 type dummy  
post-down ip link del dummy0  
~# systemctl restart networking.service  
~# ip -c -br address | grep dummy0  
dummy0           UNKNOWN        192.168.18.2/24 fe80::8852:90ff:fe0e:8159/64  
~# ip route add 192.168.123.0/24 dev dummy0               # Вариант 1, по сет. интерфейсу до перезагрузки   
$ sudo nano /etc/network/interfaces		         # Вариант 2, по адресу, с сохранением после ребута  
 #static route  
up ip ro add 192.168.235.0/25 via 192.168.18.2  
~$ ip route show  
ip route show  
default via 192.168.55.1 dev eth0 proto dhcp src 192.168.55.52 metric 100  
192.168.55.0/24 dev eth0 proto kernel scope link src 192.168.55.52  
192.168.55.1 dev eth0 proto dhcp scope link src 192.168.55.52 metric 100  
192.168.18.0/24 dev dummy0 proto kernel scope link src 192.168.18.2  
192.168.235.0/25 via 192.168.18.2 dev dummy0

***3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.***

$ ss -tn  
ESTAB            0                   64                 192.168.55.52:22              192.168.55.93:59821

TCP 22 порт(по умолчанию) использует протокол SSH, приложения – putty, terminal и т.д.

***4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?***

Netid             State              Recv-Q             Send-Q                              Local Address:Port                           Peer Address:Port             Process  
udp    UNCONN    0      0        127.0.0.53%lo:53           0.0.0.0:*              users:(("systemd-resolve",pid=567,fd=12))  
udp    UNCONN    0      0        192.168.55.52%eth0:68    0.0.0.0:*                 users:(("systemd-network",pid=384,fd=19))  
udp    UNCONN    0      0        0.0.0.0:111                  0.0.0.0:*  users:(("rpcbind",pid=565,fd=5),("systemd",pid=1,fd=36))  
udp     UNCONN   0      0        [::]:111                         [::]:*          users:(("rpcbind",pid=565,fd=7),("systemd",pid=1,fd=38))

udp 53 порт – использует DNS, служба DNS  
udp 68 порт – используется службой DHCP

***5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.***

В моем варианте диаграмма уровня L3 получится совсем простой, поэтому сделал в более красивом виде.  
https://disk.yandex.ru/i/zXEed6epKcZDiA

