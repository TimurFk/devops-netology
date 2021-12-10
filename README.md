# devops-netology

Здравствуйте, Фкирдымов Тимур комментарий к заданию 3.4


***1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter: поместите его в автозагрузку, предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),  удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.***

Скачал, распаковал, установил.  
Сделал init:  
$ sudo nano /etc/systemd/system/node_exporter.service  
[Unit]  
Description=Node Exporter Service  
_Доработка_
[Service]  
EnvironmentFile=-/etc/default/node_exporter  #в этом файле указываем переменную  
ExecStart=/usr/local/bin/node_exporter -a –n __если правильно понял, вот здесь указываем опции, которые будут передаваться в службу, в моем случае –a и -n_  
Restart=on  

[Install]  
WantedBy=default  
$ sudo systemctl enable node_exporter    # разрешаю автозапуск  
$ sudo systemctl start node_exporter #запускаю службу  
$ sudo reboot  
$ systemctl status node_exporter  
Active: active (running) since Wed 2021-12-08 10:15:17 UTC; 2h 21min ago  
Main PID: 587 (node_exporter)  
Tasks: 4 (limit: 1071)  
 Memory: 14.1M

***2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.***

По CPU:  
node_cpu_seconds_total{cpu="0",mode="idle"} 406.75                           # простой данного ядра  
node_cpu_seconds_total{cpu="0",mode="steal"} 0                               # нехватка времени на процессы VM  
node_cpu_seconds_total{cpu="0",mode="system"} 3.82                           # загрузка ядром  
node_cpu_seconds_total{cpu="0",mode="user"} 0.6                              # загрузка пользовательскими процессами  

По Ram:  
node_memory_MemAvailable_bytes 7.55789824e+08  
node_memory_MemFree_bytes 6.4924057  
node_memory_SwapCached_bytes 0

По Disk:  
node_disk_io_time_seconds_total{device="dm-0"}			 	    # время на операцию ввода-вывода  
node_disk_io_time_weighted_seconds_total{device="dm-0"} 5.764  

По network:  
node_network_transmit_drop_total{device="eth0"} 0                           # потерянные пакеты  
node_network_transmit_errs_total{device="eth0"} 0			    # кол-во ошибок  
node_network_speed_bytes{device="eth0"} 1.25e+08			    # скорость

***3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata)...***

Ознакомился, удобный и наглядный веб-интерфейс.

***4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?***

Можно с помощью команды  dmesg | grep virt  
[    0.002775] CPU MTRRs all blank - virtualized system.  
[    0.029988] Booting paravirtualized kernel on KVM  
[    0.213195] Performance Events: PMU not available due to virtualization, using software events only.  
[   10.816164] systemd[1]: Detected virtualization oracle.

***5. Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?***

$ sysctl fs.nr_open  
  fs.nr_open = 1048576   #системное ограничение открытых дескрипторов  

Другой лимит – жесткий лимит на пользователя ulimit –aH  
open files                      (-n) 1048576

***6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.***

$ sudo –i  
~# unshare -f --pid --mount-proc sleep 1h  
~# ps aux | grep sleep  
root        1181  0.0  0.0   8076   592 pts/0    S    13:45   0:00 sleep 1h  
~# nsenter --target 1181 --pid –mount  
/# ps aux  
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND  
root           1  0.0  0.0   8076   592 pts/0    S    13:45   0:00 sleep 1h  

***7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?***

(){ :|:& };: - Логическая бомба (fork bomb), вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, что в итоге приводит к её зависанию.

После запуска, ошибки довольно быстро прекратились.  
$ dmesg  
cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-1.scope

По сообщению видно, что команда выполнялась, пока не достигла лимита пользовательских процессов.  
Чтобы изменить число процессов,  делаем sudo ulimit –u 30 (например для органичения в 30)


