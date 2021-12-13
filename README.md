# devops-netology

Здравствуйте, Фкирдымов Тимур комментарий к заданию 3.6


***1. Работа c HTTP через телнет. Подключитесь утилитой телнет к сайту stackoverflow.com telnet stackoverflow.com 80 отправьте HTTP запрос. В ответе укажите полученный HTTP код, что он означает?***  
HTTP/1.1 301 Moved Permanently  
cache-control: no-cache, no-store, must-revalidate  
location: https://stackoverflow.com/questions  
Код 301 показывает, что было сделано перенаправление, если не ошибаюсь на https.

***2. Повторите задание 1 в браузере, используя консоль разработчика F12.***  
- укажите в ответе полученный HTTP код   
301 Moved Permanently  
- проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?  
Вопрос не совсем понял, напишу, как понимаю. Общее время загрузки (Load 643 мс). Если не считаем перенаправление (204 мс), то время загрузки странички - 129 мс.  
Дольше всего из общего времени грузился java script.  
https://disk.yandex.ru/i/-guLmjt_o-6qZg  
https://disk.yandex.ru/i/vdZMTHbqD3mtaA 

***3. Какой IP адрес у вас в интернете?***  
Можно узнать через браузер (yoip.ru например), но попробовал через консоль  
$ wget -qO- ident.me  
5.39.162.209

***4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois***  
$ whois -h whois.radb.net 5.39.162.209  
descr:          JSC "ER-Telecom Holding"   
origin:         AS31363

***5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute***  
$ traceroute -An 8.8.8.8  
 1  192.168.55.1 [*]  0.427 ms  0.362 ms  0.377 ms  
 2  5.39.162.193 [AS31363/AS58157]  0.902 ms  0.709 ms  0.770 ms  
 3  195.91.164.178 [AS31363/AS8331]  1.742 ms  1.974 ms  2.101 ms  
 4  86.62.124.193 [AS31363/AS8331]  1.131 ms  1.158 ms  0.965 ms  
 5  195.91.164.78 [AS31363/AS8331]  2.207 ms  2.011 ms  1.780 ms #Здесь заканчивается сети провайдера  
 6  * * *  
 7  108.170.250.129 [AS15169]  2.213 ms 172.253.69.174 [AS15169]  1.139 ms 108.170.225.36 [AS15169]  1.824 ms  
 8  * * 108.170.250.130 [AS15169]  1.921 ms  
 9  * * *  
10  209.85.254.20 [AS15169]  16.564 ms 209.85.254.6 [AS15169]  19.363 ms 108.170.232.251 [AS15169]  17.278 ms  
11  142.250.56.13 [AS15169]  13.394 ms 216.239.49.113 [AS15169]  20.335 ms 172.253.51.239 [AS15169]  17.907 ms  
12  - 149 ***  
20  * 8.8.8.8 [AS15169]  15.494 ms *

***6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?***  
$ mtr –zn 8.8.8.8  
Наибольшая задержка на участке google:

9. AS15169  172.253.66.110          0.0%    27   19.7  19.6  19.3  
10. AS15169  172.253.79.113         0.0%    75   18.8  19.0  18.7

***7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig***  
DNS сервера, отвечающие за dns.google  
$ dig +trace @8.8.8.8 dns.google  
dns.google.             10800   IN      NS      ns2.zdns.google.  
dns.google.             10800   IN      NS      ns4.zdns.google.  
dns.google.             10800   IN      NS      ns3.zdns.google.  
dns.google.             10800   IN      NS      ns1.zdns.google.

А – записи:  
$  dig +noall +answer @8.8.8.8 dns.google  
dns.google.             628     IN      A       8.8.4.4  
dns.google.             628     IN      A       8.8.8.8


***8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig***  
$ dig –x 8.8.4.4  
….  
;; QUESTION SECTION:  
;4.4.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:  
4.4.8.8.in-addr.arpa.   4650    IN      PTR     dns.google.

;; Query time: 7 msec  
…..

$ dig -x 8.8.8.8  
….  
;; QUESTION SECTION:  
;8.8.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:  
8.8.8.8.in-addr.arpa.   10793   IN      PTR     dns.google.

;; Query time: 4 msec  
…..

Доменное имя – dns.google
