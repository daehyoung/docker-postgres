## meterialized view with continuous aggregate



## 1 min by min
### meterialized view 
```
CREATE MATERIALIZED VIEW metrics_min_by_min
WITH (timescaledb.continuous) as
SELECT 
	time_bucket('1 min', created ) AS "time",
	date(created) as "metric_date",
	type_id,
	round((last(value, created) - first(value, created)) * 100.) / 100. AS value
FROM metrics
GROUP BY 1,2,3;
```

### update view every minute

```
SELECT add_continuous_aggregate_policy('metrics_min_by_min',
	start_offset => NULL,
	end_offset => INTERVAL '1 min',
	schedule_interval => INTERVAL '1 min');
```

### query
```
select
	time_bucket('15 min',time) AS bucket, 
	type_id,
	sum(value) as value 
FROM metrics_min_by_min m  
GROUP BY bucket, type_id 
order by bucket , type_id desc;
```


## hour by hour
### meterialized view 

```
drop MATERIALIZED VIEW metrics_hour_by_hour;

CREATE MATERIALIZED VIEW metrics_hour_by_hour 
  with (timescaledb.continuous) as
SELECT time_bucket('01:00:00', m.created) AS "time",
	date(m.created) as "metric_date",
       type_id,
       round((last(value, created) - first(value, created)) * 100.) / 100. AS value
FROM metrics m
GROUP BY 1,2,3;

```
### update view every hour
```
SELECT add_continuous_aggregate_policy('metrics_hour_by_hour',
	start_offset => NULL,
	end_offset => INTERVAL '1 hour',
	schedule_interval => INTERVAL '1 hour');
```

### query
```
select
time_bucket('1 day', time) AS bucket,  
type_id,
sum(value) as value 
FROM metrics_hour_by_hour m 
where m.metric_date ='2023-05-30'
GROUP BY bucket , type_id
order by bucket, type_id desc;
```

## 1 day by day
### meterialized view 
```
drop MATERIALIZED VIEW metrics_day_by_day ;

CREATE MATERIALIZED VIEW metrics_day_by_day 
WITH (timescaledb.continuous) as
SELECT 
time_bucket('1 day', created) AS "time",
date(m.created ) as "metric_date",
type_id,
round((last(value, created) - first(value, created)) * 100.) / 100. AS value
FROM metrics m
GROUP BY 1,2,3;
```

### update view every hour
```
SELECT add_continuous_aggregate_policy('metrics_day_by_day',
start_offset => NULL,
end_offset => INTERVAL '1 hour',
schedule_interval => INTERVAL '1 hour');
```

### query
```
select
time_bucket('1 month', time) AS bucket, 
type_id,
sum(value) as value 
FROM metrics_day_by_day m  
GROUP BY bucket, type_id 
order by bucket , type_id desc;
```







## query 


```
SELECT to_char(time, 'YYYY-MM-DD') as bucket,
       sum(value) as value
FROM 
(
	SELECT
	time
	,value
	FROM metrics_hour_by_hour khbh 
	ORDER BY 1
 ) a
 group by bucket
 order by bucket desc;
 ```
```
  

SELECT to_char(day, 'YYYY-MM') as bucket,
       sum(value) as value
FROM 
(
	SELECT
   	time as day
	,  value
 	FROM metrics_day_by_day 
 	ORDER BY 1
 ) a
 group by bucket
 order by bucket desc;
 ```


### remove_continuous_aggregate_policy 

```
SELECT remove_continuous_aggregate_policy('metrics_hour_by_hour');
```

```
SELECT remove_continuous_aggregate_policy('metrics_min_by_min');
```
```
SELECT remove_continuous_aggregate_policy('metrics_day_by_day');
```


## compression

```
ALTER TABLE metrics 
SET (
    timescaledb.compress, 
    timescaledb.compress_segmentby='type_id', 
    timescaledb.compress_orderby='created DESC'
);
```


```
SELECT compress_chunk(c) from show_chunks('metrics') c;

```


```
SELECT hypertable_size('metrics'); 
```

```
SELECT 
    pg_size_pretty(before_compression_total_bytes) as before,
    pg_size_pretty(after_compression_total_bytes) as after
 FROM hypertable_compression_stats('metrics');

```


## hypertable info

```
SELECT hypertable_name, hypertable_size(format('%I.%I', hypertable_schema, hypertable_name)::regclass)
  FROM timescaledb_information.hypertables;
```


```
SELECT hypertable_size('metrics_day_by_day');
```

