#Install KONG
Link reference: https://docs.konghq.com/install/ubuntu/
#Download package
```
wget -O kong-community-edition-0.14.1.xenial.all.deb https://bintray.com/kong/kong-community-edition-deb/download_file?file_path=dists/kong-community-edition-0.14.1.xenial.all.deb
```
#Install PostgreSQL 10 (required v9.5+)
Link reference: https://www.postgresql.org/download/linux/ubuntu/

Create the file /etc/apt/sources.list.d/pgdg.list and add a line for the repository
```
deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main
```
Import the repository signing key, and update the package lists
```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```
```
sudo apt-get update
sudo apt-get install postgresql-10
```

#Install KONG
```
sudo apt-get update
sudo apt-get install openssl libpcre3 procps perl
sudo dpkg -i kong-community-edition-0.14.1.xenial.all.deb
```
#
