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

Restart SSH server, change `name_user` user password:

```
sudo service ssh restart
sudo passwd name_user
reboot
```

