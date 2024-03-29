# debian_setup_moodle
## Create user, setup SSH

Connect through SSH to remote Debian server and update repositories and install some initial needed packages:

```
sudo apt-get update
sudo apt-get install -y vim mosh tmux htop git curl wget unzip zip gcc nginx git supervisor
```

Add new user
```
sudo adduser name_user
```
SSH pub_key for macOS

```
cat ~/.ssh/id_rsa.pub | pbcopy
ssh-copy-id -i ~/.ssh/id_rsa.pub user_name@host
```

Configure SSH:

```
sudo vim /etc/ssh/sshd_config
    AllowUsers name_user
    PermitRootLogin no
    PasswordAuthentication no
```
```
nano /etc/sudoers
user_name ALL=(ALL)  ALL
```
Restart SSH server, change `name_user` user password:

```
sudo service ssh restart
sudo passwd name_user
reboot
```

## Init ZSH

```
sudo apt-get install -y zsh
```

Install [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh):

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

Configure some needed aliases:

```
vim ~/.zshrc
    alias cls="clear"
```

about SHELL

```
echo $SHELL
```
## MariaDB

```
sudo apt install -y mariadb-server mariadb-client
```
```
sudo mysql_secure_installation
```
Setting for moodle

```
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
default_storage_engine = innodb
innodb_file_per_table = 1
innodb_file_format = Barracuda
innodb_large_prefix = 1

sudo service mysql restart
```
New database for moodle
```
sudo mariadb -u root -p
```
```
show databases;
CREATE DATABASE moodle;
CREATE USER 'moodleuser'@'localhost' IDENTIFIED BY 'linuxer2022';
GRANT ALL ON moodle.* TO 'moodleuser'@'localhost' IDENTIFIED BY 'linuxer2022'WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit
```
## Setting PHP
```
apt-cache policy php
sudo apt install php

```
```
php -v
```
```
sudo apt install php7.4-mysql php7.4-gmp php7.4-fpm php7.4-curl php7.4-intl php7.4-mbstring php7.4-soap php7.4-gd php7.4-zip php7.4-gd php7.4-xml php7.4-readline php7.4-opcache php7.4-json php7.4-cli php7.4-common
```
```
sudo nano /etc/php/7.4/fpm/php.ini
file_uploads = On
allow_url_fopen = On
memory_limit = 300M
upload_max_filesize = 100M
max_execution_time = 360
post_max_size = 8M
cgi.fix_pathinfo=0
```
## Install moodle
```
cd /opt
```
```
sudo git clone git://git.moodle.org/moodle.git
```
```
cd moodle
```
```
sudo git branch -a
```
```
sudo git branch --track MOODLE_311_STABLE origin/MOODLE_311_STABLE
```
```
sudo git checkout MOODLE_311_STABLE
```
```
sudo cp -R /opt/moodle /var/www/html/
```
```
cd
```
```
sudo mkdir /var/moodledata
```
```
sudo chown -R www-data /var/moodledata
```
```
sudo chmod -R 777 /var/moodledata
```
```
sudo chmod -R 777 /var/www/html/moodle
```
```
sudo chown -R www-data /var/www/html/moodle
```
```
su -
reboot
```
## Nginx conf:
```
sudo nano /etc/nginx/nginx.conf
```
```
http{
keepalive_timeout 2;
}
```
```
sudo nano /etc/nginx/sites-available/moodle
```
```
server {
listen 80;
listen [::]:80;
root /var/www/html/moodle;
index index.php index.html index.htm;
server_name 81.163.27.120;

location / {
try_files $uri $uri/ =404;
}

client_max_body_size 8M;

location /dataroot/ {
internal;
alias /var/moodledata/;
}

location ~ [^/]\.php(/|$) {
    fastcgi_split_path_info  ^(.+\.php)(/.+)$;
    fastcgi_index            index.php;
    fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    include                  fastcgi_params;
    fastcgi_param   PATH_INFO       $fastcgi_path_info;
    fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
}   

location ~* \.(ico|pdf|flv)$ {

        expires 28d;
        
    }   
    
location ~* \.(js|css|png|jpg|jpeg|gif|swf|xml|txt)$ {

        expires 7d;
}
}
```
```
sudo ln -s /etc/nginx/sites-available/moodle /etc/nginx/sites-enabled/
```
```
sudo systemctl restart nginx.service
```
```
nginx -t
Status:
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
