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
MariaDB

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
New BD for moodle
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
