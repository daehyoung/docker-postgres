
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
WITH per_hour AS (
SELECT
time,
value
FROM kwh_hour_by_hour
WHERE "time" at time zone 'Europe/Berlin' > date_trunc('month', time) - interval '1 year'
ORDER BY 1
), hourly AS (
 SELECT
      extract(HOUR FROM time) * interval '1 hour' as hour,
      value
 FROM per_hour
)
SELECT
    hour,
    approx_percentile(0.50, percentile_agg(value)) as median,
    max(value) as maximum
FROM hourly
GROUP BY 1
ORDER BY 1;
```
