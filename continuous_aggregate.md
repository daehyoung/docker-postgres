## meterialized view with continuous aggregate
## 1 min by min
### meterialized view 
```
CREATE MATERIALIZED VIEW kwh_min_by_min
WITH (timescaledb.continuous) as
SELECT 
time_bucket('1 min', created, 'Asia/Seoul') AS "time",
type_id,
round((last(value, created) - first(value, created)) * 100.) / 100. AS value
FROM metrics
GROUP BY 1,2;
```

### update view every hour
```
SELECT add_continuous_aggregate_policy('kwh_min_by_min',
   start_offset => NULL,
   end_offset => INTERVAL '1 min',
   schedule_interval => INTERVAL '1 min');
```
### query
```
 select
   time_bucket('1 min', time,'Asia/Seoul') AS bucket, 
   type_id,
	sum(value) as kwh 
FROM kwh_min_by_min khbh  
GROUP BY bucket, type_id 
order by bucket , type_id desc;
```


## hour by hour
### meterialized view 

```
drop MATERIALIZED VIEW kwh_hour_by_hour;

CREATE MATERIALIZED VIEW kwh_hour_by_hour 
  with (timescaledb.continuous) as
SELECT time_bucket('01:00:00', metrics.created, 'Asia/Seoul') AS "time",
        type_id,
       round((last(value, created) - first(value, created)) * 100.) / 100. AS value
FROM metrics 
GROUP BY 1,2;
```
### update view every hour
```
SELECT add_continuous_aggregate_policy('kwh_hour_by_hour',
   start_offset => NULL,
   end_offset => INTERVAL '1 hour',
   schedule_interval => INTERVAL '1 hour');
```
### query
```
    select
   time_bucket('1 day', time,'Asia/Seoul') AS bucket,  
   type_id,
	sum(value) as kwh 
FROM kwh_hour_by_hour khbh 
GROUP BY bucket , type_id
order by bucket, type_id desc;
```



## 1 day by day
### meterialized view 
```
CREATE MATERIALIZED VIEW kwh_day_by_day 
WITH (timescaledb.continuous) as
SELECT 
time_bucket('1 day', created, 'Asia/Seoul') AS "time",
type_id,
round((last(value, created) - first(value, created)) * 100.) / 100. AS value
FROM metrics
GROUP BY 1,2;
```

### update view every hour
```
SELECT add_continuous_aggregate_policy('kwh_day_by_day',
   start_offset => NULL,
   end_offset => INTERVAL '1 hour',
   schedule_interval => INTERVAL '1 hour');
```
### query
```
 select
   time_bucket('1 month', time,'Asia/Seoul') AS bucket, 
   type_id,
	sum(value) as kwh 
FROM kwh_day_by_day khbh  
GROUP BY bucket, type_id 
order by bucket , type_id desc;
```


