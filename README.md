# devops-netology
Здравствуйте, Фкирдымов Тимур, курсовая работа.

***1. Устанавливаем и настраиваем ufw***  
$ sudo ufw status  
ufw inactive  
$ sudo ufw default deny incoming  
$ sudo ufw default allow outgoing  
$ sudo ufw allow 22  
Rules updated  
$ sudo ufw allow 443  
Rules updated  
$ sudo ufw enable  
$ sudo ufw status  
Status: active  
….  

**#Проверяю подключение через ssh – работает.**  

***2. Установка и выпуск сертификатов с помощью hashicorp vault***  
**#Устанавливаю Vault**  
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add –  
Ok  
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"  
$ sudo apt-get update && sudo apt-get install vault  
$ sudo vault  
Usage: vault <command> [args]  
…  
$ vault server -dev -dev-root-token-id root   
$ export VAULT_ADDR=http://127.0.0.1:8200  
$ export VAULT_TOKEN=root  
$ vault status  
Key             Value  
---             -----  
Seal Type       shamir  
Initialized     true  
Sealed          false  
Total Shares    1  
Threshold       1  
Version         1.9.2  
Storage Type    inmem  
Cluster Name    vault-cluster-9caa418c  
Cluster ID      06b07fcb-d7df-40e6-b262-70f9008b7e19  
HA Enabled      false  
**#Создаю корневой сертификат**  
$ vault secrets enable pki  
2022-01-17T12:47:49.002Z [INFO]  core: successful mount: namespace="\"\"" path=pki/ type=pki  
Success! Enabled the pki secrets engine at: pki/  
$ vault secrets tune -max-lease-ttl=43800h pki  
2022-01-17T12:49:11.119Z [INFO]  core: mount tuning of leases successful: path=pki/  
Success! Tuned the secrets engine at: pki/  
$ vault write -field=certificate pki/root/generate/internal common_name="devops2021.com" ttl=43800h > CA_cert.crt  
$ vault write pki/config/urls issuing_certificates="http://127.0.0.1:8200/v1/pki/ca" crl_distribution_points="http://127.0.0.1:8200/v1/pki/crl"  
Success! Data written to: pki/config/urls  
**#Создаю промежуточный сертификат и определяю роли.**  
$ vault secrets enable -path=pki_int pki  
2022-01-17T13:10:50.171Z [INFO]  core: successful mount: namespace="\"\"" path=pki_int/ type=pki  
Success! Enabled the pki secrets engine at: pki_int/  
$ vault secrets tune -max-lease-ttl=43800h pki_int  
2022-01-17T13:12:27.581Z [INFO]  core: mount tuning of leases successful: path=pki_int/  
Success! Tuned the secrets engine at: pki_int/  
$ sudo apt install jq  
$ vault write -format=json pki_int/intermediate/generate/internal common_name="devops2021.com Intermediate Authority" | jq -r '.data.csr' > pki_intermediate.csr  
$ vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr format=pem_bundle ttl="43800h" | jq -r '.data.certificate' > intermediate.cert.pem  
$ vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem  
Success! Data written to: pki_int/intermediate/set-signed  
$ vault write pki_int/roles/devops2021-dot-com allowed_domains="devops2021.com" allow_subdomains=true max_ttl="43800h"  
Success! Data written to: pki_int/roles/devops2021-dot-com  
**#Создаю и выгружаю сертификаты**  
$ vault write pki_int/issue/devops2021-dot-com common_name="dev.devops2021.com" ttl="720h" > dev.devops2021.com.crt  
**#Далее через scp скопипровал и установил созданный корневой сертификат на хост.**  

***3. Установка и настройка nginx***  
**#Прописываю имя для localhost на хосте (win) и на виртуалке**  
$ sudo nano /etc/hosts  
…  
127.0.0.1       dev.devops2021.com  
….  
**#Устанавливаю nginx**  
$ sudo apt install nginx  
$ sudo systemctl status nginx  
● nginx.service - A high performance web server and a reverse proxy server  
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)  
     Active: active (running) since Mon 2022-01-17 14:41:45 UTC; 10s ago  
….  
**#Копирую и подготавливаю сертификаты (key и pem) для nginx и будущего скрипта**  
$ sudo systemctl stop nginx  
$ mkdir /home/vagrant/ssl  
$ cat dev.devops2021.com.crt | jq -r .data.certificate > /home/vagrant/ssl/dev.devops2021.com.crt.pem  
$ cat dev.devops2021.com.crt | jq -r .data.issuing_ca >> /home/vagrant/ssl/dev.devops2021.com.crt.pem  
$ cat dev.devops2021.com.crt | jq -r .data.private_key > /home/vagrant/ssl/dev.devops2021.com.crt.key  
**#Прописываю настройки https в nginx и закрываю вход по незащищенному 80 порту**  
$ sudo nano /etc/nginx/sites-enabled/default  
…  
server {  
        #listen 80 default_server;  
        #listen [::]:80 default_server;

        # SSL configuration  
        #  
        listen 443 ssl default_server;  
        listen [::]:443 ssl default_server;  
        ssl_certificate     /home/vagrant/ssl/dev.devops2021.com.crt.pem;  
        ssl_certificate_key /home/vagrant/ssl/dev.devops2021.com.crt.key;  
        #  
…  
$ sudo systemctl start nginx  
$ sudo systemctl status nginx  
nginx.service - A high performance web server and a reverse proxy server  
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)  
     Active: active (running) since Tue 2022-01-17 18:05:28 UTC; 6s ago  
….  
**#Проверяю, захожу с хоста на https://dev.devops2021.com**  
https://disk.yandex.ru/i/A6xtR32wImiwhw

***4. Создание скрипта генерации нового сертификата (сертификат сервера ngnix должен быть "зеленым").***  
$ nano /home/vagrant/ssl/cert-renew.sh  
#!/bin/bash  

vault write -format=json pki_int/issue/devops2021-dot-com common_name="dev.devops2021.com" ttl="720h" > /home/vagrant/ssl/dev.devops2021.com.crt  
cat /home/vagrant//ssl/dev.devops2021.com.crt | jq -r .data.certificate > /home/vagrant/ssl/dev.devops2021.com.crt.pem  
cat /home/vagrant/ssl/dev.devops2021.com.crt | jq -r .data.issuing_ca >> /home/vagrant/ssl/dev.devops2021.com.crt.pem  
cat /home/vagrant/ssl/dev.devops2021.com.crt | jq -r .data.private_key > /home/vagrant/ssl/dev.devops2021.com.crt.key  
sudo systemctl reload nginx  

$ chmod +x cert-renew.sh  
$ bash cert-renew.sh  
**#Проверяю, скрипт работает**  
https://disk.yandex.ru/i/uDdDJtcjNFfJZw

***5. Создайте скрипт crontab***  
**#Делаю скрипт на 17:20, 21 числа месяца**  
$ crontab –e  
…  
20 17 21 * * bash /home/vagrant/ssl/cert-renew.sh  

**#Проверяю выполнение скрипта**  
$ grep CRON /var/log/syslog  
Jan 21 14:20:01 vagrant CRON[4010]: (vagrant) CMD (bash /home/vagrant/ssl/cert-renew.sh)  

https://disk.yandex.ru/i/Zz5nlfk1QNgA9A   

**#Скрипт сработал по расписанию(UTC +3), сертификат обновился**
