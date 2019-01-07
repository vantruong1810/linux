# Installation
## MySQL Server
```
sudo apt-get install mysql-server
```
## Create new user
```
GRANT ALL PRIVILEGES ON *.* TO 'administrator'@'localhost' IDENTIFIED BY 'very_strong_password';
```
## Additional info
Connect database from `localhost`

Change `/etc/mysql/mysql.conf.d/mysqld.cnf`
```
bind-address = 127.0.0.1
```
to
```
bind-address = 0.0.0.0
```