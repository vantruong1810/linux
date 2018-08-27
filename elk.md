# CENTOS
Refer: https://www.howtoforge.com/tutorial/how-to-install-elastic-stack-on-centos-7/
## Prepare the OS
```
sudo vi /etc/sysconfig/selinux
```
Change SELinux value from `enforcing` to `disabled`. Reboot and check SELinux state
```
sudo
sudo getenforce
```
## Install Java 8
### Download Java 8 JRE:
```
sudo yum install java-1.8.0-openjdk # or Java 8 JDK: sudo yum install java-1.8.0-openjdk-devel
java -version
```
Please not use `java -v`, you will get error message.
```
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```
## Install and configure Elasticsearch
Refer: https://www.elastic.co/guide/en/elasticsearch/reference/current/_installation.html
### Add the elastic.co key to the server.
```
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```
### Download & Install
~Access this link to get lastest version: https://www.elastic.co/downloads/elasticsearch~
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.0.rpm
sudo rpm -ivh elasticsearch-6.4.0.rpm
```
#### NOT starting on installation, please execute the following statements to configure elasticsearch service to start automatically using systemd
```
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
```
Result: `Created symlink from /etc/systemd/system/multi-user.target.wants/elasticsearch.service to /usr/lib/systemd/system/elasticsearch.service.`
#### You can start elasticsearch service by executing
```
sudo systemctl start elasticsearch.service
```
Created elasticsearch keystore in `/etc/elasticsearch`
### Configure Elasticsearch
```
cd /etc/elasticsearch/
sudo vi elasticsearch.yml
```
Uncomment and change rows: 
```
bootstrap.memory_lock: true #This disables memory swapping for Elasticsearch. (*)
network.host: localhost
http.port: 9200
```
#### Now edit the elasticsearch.service file for the memory lock configuration [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/setting-system-settings.html#systemd "Reference"). (*)

```
sudo vim /usr/lib/systemd/system/elasticsearch.service # OR: sudo systemctl edit elasticsearch
```

Uncomment/ Add LimitMEMLOCK line.
```
[Service]
LimitMEMLOCK=infinity
```


#### Edit the sysconfig configuration file for Elasticsearch. (*)
```
sudo vim /etc/sysconfig/elasticsearch
```
Uncomment line 60 and make sure the value is `unlimited`.
```
MAX_LOCKED_MEMORY=unlimited
```
Restart Elasticsearch
```
sudo systemctl start elasticsearch
netstat -plntu # Make sure 'state' for port 9200 is 'LISTEN'.
curl -XGET 'localhost:9200/_nodes?filter_path=**.mlockall&pretty' # Check: mlockall = true
curl -XGET 'localhost:9200/?pretty' # Check "tagline" : "You Know, for Search"
```
NOTE: (*): https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration-memory.html
## Install and configure Kibana
