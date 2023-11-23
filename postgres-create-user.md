

# create user and database
```
sudo -u postgres
createuser pms
createdb pms
psql
postgres=# alter user pms with encrypted password 'pms12';
postgres=# grant all privileges on database pms to pms;
```

# note
```
sudo -u postgres psql
postgres=# create database mydb;
postgres=# create user myuser with encrypted password 'mypass';
postgres=# grant all privileges on database mydb to myuser;

Creating user
$ sudo -u postgres createuser <username>
Creating Database
$ sudo -u postgres createdb <dbname>
Giving the user a password
$ sudo -u postgres psql
psql=# alter user <username> with encrypted password '<password>';
Granting privileges on database
psql=# grant all privileges on database <dbname> to <username> ;
```
