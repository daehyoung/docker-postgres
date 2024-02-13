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

## Create continuous aggregates
### day by day
#### Create a continuous aggregate 
```
CREATE MATERIALIZED VIEW kwh_day_by_day(time, value)
    with (timescaledb.continuous) as
SELECT time_bucket('1 day', created, 'Asia/Seoul') AS "time",
        round((last(value, created) - first(value, created)) * 100.) / 100. AS value
FROM metrics
WHERE type_id = 5
GROUP BY 1;
```

#### Add a refresh policy to keep the continuous aggregate up-to-date
* add 

```
SELECT add_continuous_aggregate_policy('kwh_day_by_day',
   start_offset => NULL,
   end_offset => INTERVAL '1 hour',
   schedule_interval => INTERVAL '1 hour');
```

* remove
  
``
SELECT remove_continuous_aggregate_policy('kwh_day_by_day');
``

### kwh_hour_by_hour
#### Create a continuous aggregate 
```
CREATE MATERIALIZED VIEW kwh_hour_by_hour(time, value)
  with (timescaledb.continuous) as
SELECT time_bucket('01:00:00', metrics.created, 'Asia/Seoul') AS "time",
       round((last(value, created) - first(value, created)) * 100.) / 100. AS value
FROM metrics
WHERE type_id = 5
GROUP BY 1;
```
#### Add a refresh policy to keep the continuous aggregate up-to-date
```

SELECT add_continuous_aggregate_policy('kwh_hour_by_hour',
   start_offset => NULL,
   end_offset => INTERVAL '1 hour',
   schedule_interval => INTERVAL '1 hour');
```

### check result
```
SELECT view_name, format('%I.%I', materialization_hypertable_schema,materialization_hypertable_name) AS materialization_hypertable
FROM timescaledb_information.continuous_aggregates;

```

## query
```
select * from kwh_hour_by_hour where time > TO_TIMESTAMP(  '2023-05-29 00:00:00' , 'YYYY-MM-DD HH24:MI:SS' ) AT TIME ZONE 'Asia/Seoul' 
and time < ( TO_TIMESTAMP(  '2023-05-29 00:00:00' , 'YYYY-MM-DD HH24:MI:SS' ) AT TIME ZONE 'Asia/Seoul'  + INTERVAL '1 DAY' ) limit 10;


```
