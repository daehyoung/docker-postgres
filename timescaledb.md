## install
### create volume

```
sudo mkdir postgres_data
sudo chown 70:root postgres_data
```


```
docker volume create --driver local \
 --name postgres_data \
 --opt type=none \
 --opt device=/home/hms/data/postgres_data \
 --opt o=bind,uid=70
```

docker-compose.yml
```
services:
  db:
    image: timescale/timescaledb:latest-pg14
    environment:
      TZ: Asia/Seoul
      POSTGRES_DB: timescale
      POSTGRES_USER: timescale
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```


### run docker
```
docker run -d --name timescaledb  \
  -p 5432:5432 \
  -v postgres_data:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=postgres \
  -e TZ=Asia/Seoul \
  timescale/timescaledb:latest-pg14

```

### create database
```
create database example;
grant all privileges on database example to postgres;

```
```
ALTER DATABASE example SET timezone TO 'Asia/Seoul';
```

```
  SHOW data_directory;
      data_directory      
--------------------------
 /var/lib/postgresql/data
(1 row)

```

## Create a standard PostgreSQL table 
```
CREATE TABLE "metrics"(
    created timestamp with time zone default now() not null,
    type_id integer                                not null,
    value   double precision                       not null
);

```

## Convert the standard table into a hypertable 

```
SELECT create_hypertable('metrics', by_range('created'));
```

## setup data
### download
https://assets.timescale.com/docs/downloads/metrics.csv.gz

### insert data into table
```
\COPY metrics FROM metrics.csv CSV;
```

```
SELECT * FROM metrics LIMIT 5;
``` 
 
