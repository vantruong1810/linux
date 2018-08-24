# UBUNTU 16.04
```bash
apt-get update
```

## Nginx
```bash
apt-get install nginx -y
```
## Mysql
```bash
apt-get install mysql-server
mysql -u root -p{your_password}
```
### Create new user Mysql
```mysql
CREATE USER '<user_name>'@'%' IDENTIFIED BY '<password>';
GRANT ALL PRIVILEGES ON *.* TO '<user_name>'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
### Note for issue not connect from client to server
#### Run command
```bash
vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
#### Change config
```yaml
bind_address = 0.0.0.0
```

## PHP
```bash
add-apt-repository ppa:ondrej/php
apt-get install software-properties-common
apt-get install python-software-properties -y
apt-get update
apt-get -y install php7.2 php7.2-mysql php7.2-fpm
```
### PHP package optional
```bash
apt-get -y install php7.2-mbstring php7.2-xml php7.2-curl php7.0-soap php7.2-common php7.2-cli
```
### Config PHP
```bash
vi /etc/php/7.2/fpm/php.ini
```
#### Config PHP  like this
```bash
cgi.fix_pathinfo=0
upload_max_filesize = 128M
post_max_size = 128M
```
### Config NGINX accept PHP
```bash
vi /etc/nginx/sites-available/default
```
#### Config NGINX  like this
```yaml
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name server_domain_or_IP;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
## Zip and Unzip
```bash
apt-get install zip
apt-get install unzip
```
## Composer
```bash
sudo curl -s https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```