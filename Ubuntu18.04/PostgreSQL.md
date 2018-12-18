# Installation
## Create repository
Create the file `/etc/apt/sources.list.d/pgdg.list` and add a line for the repository
```
deb http://apt.postgresql.org/pub/repos/apt/ bionic-pgdg main
```
## Import the repository signing key, and update the package lists
```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
```
## Install `posgresql-10`
```
sudo apt-get install postgresql-10
```
## Setting a password for the postgres user
Run the psql command from the postgres user account:
```
sudo -u postgres psql postgres
```
Set the password:
```
\password postgres
Enter a password.
```
Close psql.
```
\q
```