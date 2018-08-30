# CENTOS
Refer: https://www.howtoforge.com/tutorial/how-to-install-elastic-stack-on-centos-7/
## 1. Prepare the OS
```
sudo vi /etc/sysconfig/selinux
```
Change SELinux value from `enforcing` to `disabled`. Reboot and check SELinux state
```
sudo
sudo getenforce
```
## 2. Install Java 8
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
## 3. Install and configure Elasticsearch
Refer: https://www.elastic.co/guide/en/elasticsearch/reference/current/_installation.html
### Add the elastic.co key to the server.
```
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```
### Download & Install
Access this link to get lastest version: https://www.elastic.co/downloads/elasticsearch
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
Uncomment and change below rows: 
```
bootstrap.memory_lock: true #This disables memory swapping for Elasticsearch. (*)
network.host: localhost # 0.0.0.0 to login by both IP Adress
http.port: 9200
```
#### Now edit the elasticsearch.service file for the memory lock configuration [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/setting-system-settings.html#systemd "Reference"). (*)

```
sudo systemctl edit elasticsearch # OR: sudo vim /usr/lib/systemd/system/elasticsearch.service
```

Uncomment/ Add `LimitMEMLOCK` line.
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
*NOTE:* (*): https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration-memory.html
If can't access from outside to port `9200`, please run `firewall-cmd --list-all`

Do you see port `9200/tcp` listed? If not, can you run `firewall-cmd --permanent --add-port=9200/tcp` and `firewall-cmd --reload` and check again? 
## 4. Install and configure Kibana
### Intstall Kibana on another server [Optional]
Add the elastic.co key to the server.
```
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```
### Download & Install
Access this link to get lastest version: https://www.elastic.co/downloads/kibana
```
wget https://artifacts.elastic.co/downloads/kibana/kibana-6.4.0-x86_64.rpm
sudo rpm -ivh kibana-6.4.0-x86_64.rpm
```
### Now edit the Kibana configuration file.
```
sudo vim /etc/kibana/kibana.yml
```
Uncomment the configuration lines for server.port, server.host and elasticsearch.url.
```
server.port: 5601
server.host: "localhost"
elasticsearch.url: "http://xxx.xxx.xxx:9200" # localhost:9200 for elasticsearch is installed same server
```
Start kibana
```
sudo systemctl daemon-reload
sudo systemctl enable kibana.service
sudo systemctl start kibana.service
```
*Note:*

If can't access from outside to port `5601`, please run `firewall-cmd --list-all`

Do you see port `5601/tcp` listed? If not, can you run `firewall-cmd --permanent --add-port=5601/tcp` and `firewall-cmd --reload` and check again? 

## 5. Install and configure Logstash
### Install Java 8 on another server [Here](https://github.com/vantruong1810/linux/blob/master/elk.md#install-java-8 "Install Java 8") [Optional]
### Intstall Logstash on another server [Optional]
Add the elastic.co key to the server.
```
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```
### Download & Install
Access this link to get lastest version: https://www.elastic.co/downloads/logstash
```
wget https://artifacts.elastic.co/downloads/logstash/logstash-6.4.0.rpm
sudo rpm -ivh logstash-6.4.0.rpm
```
### Generate a new SSL
Generate a new SSL certificate file so that the client can identify the elastic server.
```
cd /etc/pki/tls
sudo vim openssl.cnf
```
Find the `[ v3_ca ]` section in the file and add
```
[ v3_ca ]

# Server IP Address
subjectAltName = IP: xx.xx.xx.xx # ELK_server_private_IP
```
Generate the certificate file with the openssl command.
```
sudo openssl req -config /etc/pki/tls/openssl.cnf -x509 -days 3650 -batch -nodes -newkey rsa:2048 -keyout /etc/pki/tls/private/logstash-forwarder.key -out /etc/pki/tls/certs/logstash-forwarder.crt
```
The certificate files can be found in the `/etc/pki/tls/certs/` and `/etc/pki/tls/private/` directories.
### Create logstash configuration files
_Filebeat_
```bash
cd /etc/logstash/
sudo vim conf.d/filebeat-input.conf
```
Paste
```yaml
input {
  beats {
    port => 5443
    ssl => true
    ssl_certificate => "/etc/pki/tls/certs/logstash-forwarder.crt"
    ssl_key => "/etc/pki/tls/private/logstash-forwarder.key"
  }
}
```
_Syslog_
```bash
cd /etc/logstash/
sudo vim conf.d/syslog-filter.conf
```
Paste
```yaml
filter {
  if [type] == "syslog" {
    grok {
      match => { "message" => "%{SYSLOGTIMESTAMP:syslog_timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?: %{GREEDYDATA:syslog_message}" }
      add_field => [ "received_at", "%{@timestamp}" ]
      add_field => [ "received_from", "%{host}" ]
    }
    date {
      match => [ "syslog_timestamp", "MMM  d HH:mm:ss", "MMM dd HH:mm:ss" ]
    }
  }
}
```

_Output elasticsearch_
```bash
cd /etc/logstash/
sudo vim conf.d/output-elasticsearch.conf
```
Paste
```yaml
output {
  elasticsearch {
    hosts => "xxx.xxx.xxx.xxx:9200"
    manage_template => false
    index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
    document_type => "%{[@metadata][type]}"
  }
}
```

Start logstash
```
sudo systemctl daemon-reload
sudo systemctl enable logstash.service
sudo systemctl start logstash.service
```
*Note:*

If can't access from outside to port `5443`, please run `firewall-cmd --list-all`

Do you see port `5443/tcp` listed? If not, can you run `firewall-cmd --permanent --add-port=5443/tcp` and `firewall-cmd --reload` and check again? 

## 6. Install filebeat on ubuntu
### Make directory
```
sudo mkdir -p /etc/pki/tls/certs/
```
Copy `/etc/pki/tls/certs/logstash-forwarder.crt` from logstash server to `/etc/pki/tls/certs/logstash-forwarder.crt`
### Add the elastic key to the server.
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
### Install filebeat
Access this link to get lastest version: https://www.elastic.co/downloads/beats/filebeat
```
wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-6.4.0-amd64.deb
sudo dpkg -i filebeat-6.4.0-amd64.deb
```
### Configure filebeat
```
cd /etc/filebeat/
sudo vim filebeat.yml
```
