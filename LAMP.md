# Agenda
* [Apache 2.4](##apache-24)
* [PHP 7.2](#php-72)
* [MySQL 8.0](#mysql-80)
## Apache 2.4
* Download link: https://www.apachelounge.com/download/

Unzip and copy to `C:\Apache24`
* Configuration: `C:\Apache24\conf\httpd.conf`
Open & edit:  
```yaml
LoadModule rewrite_module modules/mod_rewrite.so
...
<Directory "c:/Apache24/htdocs">
...
AllowOverride All
...
</Directory>
```
* Run:
Open `Command Prompt` and run
```
cd C:\Apache24\bin
httpd -k install
net start Apache2.4
```
* Testing: Run `localhost` and see this test `It works`
## PHP 7.2
* Download link: http://windows.php.net/download/ (Thread Safe)
* Configuration: 
1. Unzip and copy to `C:\php`
2. Copy `php.ini-production` to `php.ini`
3. Edit `php.ini`
```yaml
extension_dir = "ext"
...
extension=bz2
extension=curl
extension=fileinfo
...
extension=mysqli
...
extension=soap
```
4. Add to `C:\Apache24\conf\httpd.conf`
```yaml
AddType application/x-httpd-php .php
<FilesMatch \.php$>
  #SetHandler application/x-httpd-php
  AddHandler application/x-httpd-php .php
  LoadModule php7_module "c:/php/php7apache2_4.dll"
</FilesMatch>
PHPIniDir "c:/php"

```
5. Testing:

i. Restart Apache
```bash
# start Apache
net start Apache2.4

# stop Apache
net stop Apache2.4
```
ii. Create file `php` to check: 

Eg: ```<?php phpinfo(); ?>```
* XDebug:

Download [php_xdebug](https://xdebug.org/download.php) (Thread Safe)
```yaml
[XDebug]
zend_extension = "c:\php72\ext\php_xdebug-2.6.0-7.2-vc15-x86_64.dll"
;xdebug.remote_autostart = 1
;xdebug.profiler_append = 0
;xdebug.profiler_enable = 0
;xdebug.profiler_enable_trigger = 0
;xdebug.profiler_output_dir = "c:\xampp\tmp"
;xdebug.profiler_output_name = "cachegrind.out.%t-%s"
xdebug.remote_enable = 1
xdebug.remote_handler = "dbgp"
xdebug.remote_host = "127.0.0.1"
xdebug.remote_log="c:\php72\tmp\xdebug.txt"
xdebug.remote_port = 9000
```
## MySQL 8.0
* Download link: https://dev.mysql.com/downloads/installer/

Please only install `MySQL Server 8.0` and `MySQL Workbench`
* Configuration: 

Should create new user with `mysql_native_password` like as below query:
```bash
CREATE USER 'newuser'@'localhost'
  IDENTIFIED WITH mysql_native_password BY 'password'
```
## PhpMyAdmin