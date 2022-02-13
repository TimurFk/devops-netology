# devops-netology
Здравствуйте, Фкирдымов Тимур, работа 5.2.

***Задача 1. Опишите своими словами основные преимущества применения на практике IaaC паттернов. Какой из принципов IaaC является основополагающим?***

Преимущества IaaC:  
- позволяет быстрее конфигурировать, развертывать и масштабировать инфраструктуру, что увеличивает общую скорость работы команды, ресурсы не простаивают.  
- обеспечивает повторяемость разворачиваемой инфраструктуры, стандартизирует её, благодаря чему позволяет избежать несовпадения сред разработки и увеличивает безопасность.  
- упрощает и ускоряет восстановление в случае проблем, за счёт повторного развертывания последней работоспособной конфигурации.

Основной принцип Iaac – Идемпотентность, при повторном выполнении операции мы получаем результат, идентичный предыдущему и последующим.

***Задача 2. Чем Ansible выгодно отличается от других систем управление конфигурациями? Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?***

Главный плюс Ansible, что он использует SSH, не требуя, в отличии от других, установки специального окружения. Также имеет множество модулей в поставке.  
Метод Push мне кажется более надежным. Во-первых, управляющий сервер в одно и тоже время может отправлять конфигурацию всем, нет проблемы, что машины получат её в разное время, в зависимости от времени их запроса (как пример срочно нужно отправить исправление).   
Во-вторых, управляющий сервер контролирует процесс отправки, получения и установки конфигурации со своей стороны, то есть мониторинг в варианте push более простой, особенно, если нужно применить несколько конфигураций подряд.

***Задача 3. Установить на личный компьютер: VirtualBox, Vagrant, Ansible***

#Virtual Box уже установлен, проверяю.  
> cd C:\Program Files\Oracle\VirtualBox  
> VBoxManage.exe list vms  
"server1.netology" {000c7d6a-3340-4421-bc07-7f2cf6629934}

#Проверяем работу Vagrant  
>vagrant global-status  
id       name             provider   state   directory  
---------------------------------------------------------------------------------  
b680cfe  server1.netology virtualbox running D:/virtbox/Ubuntu  

#Проверяем Ansible  
$ ansible –version  
ansible 2.9.6  
  config file = /etc/ansible/ansible.cfg  
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']  
  ansible python module location = /usr/lib/python3/dist-packages/ansible  
  executable location = /usr/bin/ansible  
  python version = 3.8.10 (default, Jun  2 2021, 10:49:15) [GCC 9.4.0]



