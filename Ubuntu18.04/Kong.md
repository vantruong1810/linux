#  Install KONG
Link reference: https://docs.konghq.com/install/ubuntu/
# Download package
```
wget -O kong-community-edition-1.0.0.bionic.all.deb https://bintray.com/kong/kong-community-edition-deb/download_file?file_path=dists/kong-community-edition-1.0.0.bionic.all.deb
```
# Install PostgreSQL 10 (required v9.5+)
Link reference: https://www.postgresql.org/download/linux/ubuntu/

Create the file `/etc/apt/sources.list.d/pgdg.list` and add a line for the repository
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

# KONG
## Install
```
sudo apt-get update
sudo apt-get install openssl libpcre3 procps perl # Optional
sudo dpkg -i kong-community-edition-0.14.1.bionic.all.deb
```
## Create DB

```
sudo -u postgres psql
CREATE USER kong; CREATE DATABASE kong OWNER kong;
ALTER USER kong WITH PASSWORD 'password'; #change password for kong user
\q
```

## Configuration
```
sudo cp /etc/kong/kong.conf.default /etc/kong/kong.conf
sudo vi /etc/kong/kong.conf
```
### Change something like as: (uncomment these lines)
```
database = postgres             # Determines which of PostgreSQL or Cassandra
                                # this node will use as its datastore.
                                # Accepted values are `postgres` and
                                # `cassandra`.

pg_host = 127.0.0.1             # The PostgreSQL host to connect to.
pg_port = 5432                  # The port to connect to.
pg_user = kong                  # The username to authenticate if required.
pg_password = password          # The password to authenticate if required.
pg_database = kong              # The database name to connect to.
```
### Migrate data
```
kong migrations up
```
OR 
```
kong migrations up -c /etc/kong/kong.conf
```
Note: 
```yaml
Error: cannot run migrations: database needs bootstrapping; run 'kong migrations bootstrap'
```
## Start Kong
```
kong start [-c /etc/kong/kong.conf] # Actions: start/stop/reload
```
## Check status
```
curl -i http://localhost:8001/
```
