

# create user and database
```
sudo -u postgres
createuser pms
createdb pms
psql
postgres=# alter user pms with encrypted password 'pms12';
postgres=# grant all privileges on database pms to pms;
```
# remote login
```
postgres=# SHOW hba_file;
            hba_file             
---------------------------------
 /var/lib/pgsql/data/pg_hba.conf
(1개 행)
``` 
```
postgres=# SHOW config_file;
             config_file             
-------------------------------------
 /var/lib/pgsql/data/postgresql.conf
(1개 행)
```

##  postgresql.conf
`/var/lib/pgsql/data/postgresql.conf`
```
listen_addresses = '*'          # what IP address(es) to listen on;
```

## pg_hba.conf
```sudo vi /var/lib/pgsql/data/pg_hba.conf```
```
# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5


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
