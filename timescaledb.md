
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

