# [A Complete Guide to PostgreSQL Performance Tuning: Key Optimization Tips DBAs Should Know](https://sematext.com/blog/postgresql-performance-tuning/)

POSTED ON [NOVEMBER 29,
2022](https://sematext.com/blog/postgresql-performance-tuning/) BY
[SUNNY SRINIDHI](https://sematext.com/blog/author/sunny-srinidhi/)
 

- [A Complete Guide to PostgreSQL Performance Tuning: Key Optimization Tips DBAs Should Know](#a-complete-guide-to-postgresql-performance-tuning-key-optimization-tips-dbas-should-know)
  - [What Is PostgreSQL Performance Tuning?](#what-is-postgresql-performance-tuning)
  - [How to Tune PostgreSQL Performance](#how-to-tune-postgresql-performance)
  - [Database Design](#database-design)
  - [Hardware Tuning](#hardware-tuning)
    - [CPU](#cpu)
    - [RAM](#ram)
    - [Disk I/O](#disk-io)
    - [Network](#network)
  - [Operating System Optimization](#operating-system-optimization)
  - [Database Configuration Parameters Tuning](#database-configuration-parameters-tuning)
    - [Connection Configuration Parameters](#connection-configuration-parameters)
    - [Memory Configuration Parameters](#memory-configuration-parameters)
    - [Write-Ahead Log Configuration Parameters](#write-ahead-log-configuration-parameters)
    - [Query Cost Configuration Parameters](#query-cost-configuration-parameters)
  - [Logging](#logging)
  - [Vacuum](#vacuum)
  - [Query Performance Tuning](#query-performance-tuning)
    - [ANALYZE](#analyze)
    - [EXPLAIN](#explain)
    - [Indexing Performance](#indexing-performance)
  - [PostgreSQL Performance Tuning Tools](#postgresql-performance-tuning-tools)
  - [Monitor and Tune PostgreSQL with Sematext](#monitor-and-tune-postgresql-with-sematext)


PostgreSQL is an open-source relational database that is highly flexible and reliable and offers a varied set of features. Even though it is a complex database, it provides great integrity and performance. Also, you
can deploy it on multiple platforms, including a light version for websites and smartphones.

Because you can deploy Postgres in different ways, it comes out of the box with only some basic performance tuning based on the environment you're deploying on. So it's important that once you deploy the database, you make sure to tune it according to your use case to get the best performance possible.

In this article I'll cover ```performance tuning and optimization best practices that will help you speed up your PostgreSQL queries```.

## What Is PostgreSQL Performance Tuning?

PostgreSQL performance tuning is the process of changing the configuration in an effort to get better performance out of your database. This requires a thorough understanding of how the database works, what each configuration parameter does, and what values need to be used.

Tuning PostgreSQL for performance is not similar to tuning other databases. This is because, with PostgreSQL, you can turn on each schema for a different metric of performance based on the use case, for example, either frequent writes or frequent reads.

## How to Tune PostgreSQL Performance

There are many ways to tune a PostgreSQL database's performance. It mostly depends on the use case, so it's important to know what you want to achieve before you start. There are multiple configuration parameters that can be used for tuning, some of which I'll discuss in this section.

## Database Design

Designing the database is arguably one of the most important steps in optimizing the performance of any database, not just PostgreSQL. Because PostgreSQL offers relational database properties, you can easily ```partition the data into multiple logically separated tables instead``` of having one big table. This usually shows immediate and significant improvement in query performance.

Another common and obvious way of optimizing PostgreSQL performance is by ```having enough indexes```. This again depends heavily on the use case and the queries you'll be running often. The idea here is to filter as much data as possible so that there's less data to work with. So, you  should ```create the indexes on columns``` that are typically used as filters in the most frequently run queries.

Even though indexes help in improving query performance, make sure to use them with caution. Overusing indexes will cause adverse effects.
Creating and maintaining indexes is a costly operation, and creating too many indexes will deteriorate the overall performance of the database.

## Hardware Tuning

The underlying hardware definitely has its part to play in optimizing the performance of PostgreSQL. Developers need to take into account data partitioning, indexing, configuration, and hardware capacity while designing queries. Here, we'll look at four major hardware components and how they affect PostgreSQL performance.

### CPU

The CPU plays a major role in the performance of PostgreSQL queries.
Complex operations and computations such as aggregations, joins, hashing, grouping, sorting, etc. require CPU time. And along with CPU time, the CPU should be capable enough to handle such tasks.

Scaling up CPU is expensive. So you need to perform a thorough analysis of the impact of the CPU on performance before a CPU upgrade. At the same time, enough thought has to be put into optimizing the query to make full use of the existing CPU.

### RAM

RAM or memory is crucial for databases to function properly. Whenever we submit a query, the data is first brought into memory, and then any aggregation, grouping, sorting, etc. is applied on the data. So it's important to make sure you have enough memory to hold your data.

With increased memory, you'll also see increased disk cache and reduced I/O operations on the disk. This improves PostgreSQL query performance significantly, as I/O operations are a lot more expensive than operations in memory. It's never bad to have a little more memory than what's absolutely necessary.

On the other hand, if we don't have enough memory, our queries could be killed with an "out of memory" exception, or other running processes could be killed abruptly to accommodate our queries.

### Disk I/O

A hard disk is where all the data resides. So whenever a query is submitted, the data should first be read from the disk and loaded into memory. Similarly, whenever there is a write operation, the data must be written to disk from memory. All these incur disk I/O costs.

A fast read and write time greatly improves the performance of a PostgreSQL query, as data can be quickly loaded into the memory or quickly off-loaded from memory. If there are significant I/O operations on the database, another good idea is to physically store each tablespace on a different disk drive so that the disk is not overloaded with I/O operation requests.

### Network

Optimizing the network might not seem that important in the beginning.
But as the data grows, you can see the network playing an important role in query performance. Network delays are one of the most common performance bottlenecks. Having reliable, high-speed, and high-bandwidth network cards is important. Also, high-speed connectivity between nodes is equally crucial. This allows for data to flow easily and quickly between machines and not block queries.

## Operating System Optimization

The operating system plays a major role in the performance of a database since it's the layer that enables communication between the database software and the underlying hardware. For example, if an operating system is not able to read huge parts of data files from the disk to memory, overall performance will be poor.

Every operating system provides many configuration parameters to tune performance to better suit our use case. With a customized configuration, we can significantly improve the read and write performance of our PostgreSQL databases. Operating systems also provide capabilities that database software don't usually ship with, and yet, they rely on such features for proper functioning.

An example of this is keeping a TCP connection active and open between a database server and a client. I usually see errors like the following with PostgreSQL databases:

- could not receive data from client: Connection reset by peer

- server closed the connection unexpectedly

These errors occur when a connection between a server and a client is lost. There could be many causes for this failure, but the most common reason is that the TCP socket is closed. Whenever a connection is idle for a specified amount of time, the connection gets terminated automatically.

To avoid this, each operating system's kernel has mechanisms to periodically send "keepalive" signals to the other system to make sure the connection is open. Because this feature is provided by the operating system kernel and not the database, PostgreSQL uses this for a reliable connection between various systems.

By default, both Windows and Linux systems come with a configured keepalive idle time of 2 hours. Linux sends a keepalive signal every 75 seconds, and Windows sends the same signal every second. You can tune these configuration parameters to better suit your requirements.

## Database Configuration Parameters Tuning

PostgreSQL comes with a bunch of configuration parameters to optimize its performance.

### Connection Configuration Parameters

```max_connections``` affects how the PostgreSQL server and client connections behave. You can use this to configure the maximum number of parallel connections that a PostgreSQL server can support. The formula used to calculate this is:
```
max_connections = max(4 * number of CPU cores, 100)
```
This makes sure that at any given point in time, the CPU is not overloaded with too many active connections. But we also need to make sure we have enough hardware resources to support this number of parallel connections.

### Memory Configuration Parameters

The following configuration parameters govern how system memory is used by various processes and features of the PostgreSQL database. It's important to note that all memory-related configurations combined should not exceed the maximum amount of memory available.

```shared_buffers``` determines the amount of memory that can be used by PostgreSQL for shared memory buffers. It's a good rule of thumb to allocate no more than 25% of the available memory for this.

```temp_buffers``` determines the amount of memory used for temporary buffers for each database session. Because this is local to each session, you should multiply this with the number of active sessions to get the maximum amount it can reach.

```wal_buffers``` is the amount of shared_buffers memory that will be used to buffer WAL data before it can be written to disk. By default, the value is set to -1, equivalent to around 3% of the shared_buffers size.
This cannot be less than 64 KB or more than the size of one WAL segment, which is typically 16 MB.

```work_mem``` is the maximum amount of memory a query can use for its operation, such as sorts, hash tables, etc. Note that this limit is per query and not for the entire database. By default, the limit is set to 4 MB, which can be changed to better suit your use cases.

```maintenance_work_mem``` is the amount of memory allocated to perform maintenance activities on the database, like creating indexes, altering tables, vacuuming, data loading, etc. Usually, when these operations are being performed, there's an increase in disk I/O operations, as the changes have to be written to disk. The optimal way for this would be to perform as many operations as possible in memory and write the final
output to disk, thereby reducing the expensive disk I/O operations. So having at least 1 GB of memory for maintenance work is a good starting point.

### Write-Ahead Log Configuration Parameters

Write-Ahead Log (WAL) makes sure all changes to the data are logged before changing the data itself so that in case of failures the log can be replayed to recover the data changes. The following are some of the configuration parameters related to WAL that can yield performance improvements for a PostgreSQL database.

```fsync``` makes sure all updates to the data are first written to disk.
This is a measure to recover data after either a software or hardware crash. As you can imagine, these disk write operations are expensive, and could negatively affect performance. But this also makes sure data integrity is maintained, a tradeoff depending on the use case.

```commit_delay``` sets the amount of time for which a WAL flush has to wait before flushing the log to the disk. This way, more than one transaction can be flushed to the disk at once, improving the overall performance. But make sure it's not too long, or else a significant number of transactions could be missed in case of a crash.

```heckpoint_timeout``` indicates the maximum amount of time between two WAL checkpoints. The range is 30 seconds to 1 day, and the default value is 5 minutes. I need to note here that if this value is significantly large, the time taken for data recovery after a crash is also going to be longer.

```checkpoint_completion_target``` tells how the checkpoint writes need to be completed within the checkout_timeout interval. The default value is 0.9, which means that the writes to disk will be distributed across 90% of the time between two checkpoints. This way, the I/O operations are not overloading the CPU and causing issues. There's an even distribution of write operations, which is expected. Also, this way, there's enough time for the overhead as well. Changing this value, however, is not
recommended.

```checkpoint_segments``` is the maximum number of segments there can be between checkpoints. By default, there will be three segments, and each segment will be 16 MB. Increasing the number of segments will also increase the time taken for data recovery after a crash.

checkpoint_segments is deprecated for version 9.5 and higher, having been replaced with [[min_wal_size]](https://www.postgresql.org/docs/9.5/runtime-config-wal.html#GUC-MIN-WAL-SIZE)
and [[max_wal_size]](https://www.postgresql.org/docs/9.5/runtime-config-wal.html#GUC-MAX-WAL-SIZE).
If you previously adjusted checkpoint_segments, the following formula
will give you an approximately equivalent setting:
```
max_wal_size = (3 * checkpoint_segments) * 16MB
```
Note that the default setting for max_wal_size is much higher than the default checkpoint_segments used to be, so adjusting it might no longer be necessary.

### Query Cost Configuration Parameters

The query planner uses a variety of configuration parameters and signals to calculate the cost of every query. Some of these parameters are listed below and can potentially improve the performance of a PostgreSQL query.

```random_page_cost``` sets the cost for the planner for fetching a disk page non-sequentially. The default value is 4. You can configure this at the tablespace level as well. Reducing the value to less than 4 will make the planner prefer index scans. Increasing it will tell the planner that index scans are more expensive.

```effective_cache_size``` is an estimate of the effective size of disk cache available for each query. This cost adds to the expense of either using an index scan or a sequential scan. The default value here is 4 GB, which you can modify.

## Logging

PostgreSQL offers some configuration parameters for
[[logging]](https://sematext.com/guides/log-management/) too, which can help improve the maintenance of a PostgreSQL database.

```logging_collector``` lets you catch all the log messages sent to the standard error logger and then redirects those to a [[logfile]](https://sematext.com/glossary/log-file/). This way, we can be sure that all log messages are captured, even those that don't appear in the [[syslog]](https://sematext.com/blog/what-is-syslog-daemons-message-formats-and-protocols/).

```log_statement``` controls what types of queries are logged into log
files. The options here include none, ddl, mod, and all. DDLs are
queries that are used to create, alter, or drop tables. MOD queries are
used to insert, update, delete, truncate, and other similar operations
on a table.

```log_min_error_statement``` sets the minimum [[logging
level]{.ul}](https://sematext.com/blog/logging-levels/) at which SQL queries generating such errors are logged into the system log. Values include DEBUG1, DEBUG2, INFO, NOTICE, WARNING, ERROR, FATAL, etc.

```log_line_prefix``` is a printf-style string you can prefix at the start of each log line. The % character begins the escape sequence. This is then replaced with status information depending on the option provided.
There are options to pad the log prefix either to the right or the left.

```log_lock_waits``` controls whether a log message will be produced after the deadlock_timeout period is elapsed. This gives a clear indication of whether lock waits are the reason for poor performance. By default, this configuration is turned off and requires superuser permission to be turned on.

```log_checkpoints``` logs checkpoints and restart points to the server log. Along with this, the number of buffers written and the time taken to write these buffers are also included in the log message, allowing for better understanding and debugging.

## Vacuum

In PostgreSQL, when a row or tuple is updated or deleted, the record is not actually physically deleted or altered. This leaves obsolete records on the disk, which consume disk space and also negatively affect query performance. To fix this, PostgreSQL provides a neat feature called Vacuum that lets me easily clear out such records from the disk and gain back the space, improving query performance too.

The Vacuum command comes with many options. For example, you can use the VACUUM ANALYZE command to first physically delete obsolete records; then, automatically run the ANALYZE operation to refresh the stats of a table for the query planner to better plan future queries on that table.

Or, you can use the VACUUM FULL command to do more with the freed-up space. The Vacuum command itself will first delete the records from the table and keep the newly acquired free disk space with the table itself for future use, for when the table grows. This will not release the disk space back to the operating system. But using the FULL option with the command will actually rewrite the complete table into a new disk file; whatever disk space is freed up will be given back to the operating system for other processes to use. Because of this, VACUUM FULL can't be used in parallel to any other read or write operation on the table. Only the VACUUM command can be run in parallel.

Given how important the Vacuum process is, PostgreSQL ships with an automated version of this called the autovacuum. The autovacuum process comprises various modules that work in tandem to automatically run the Vacuum command at regular intervals to make sure all obsolete records are deleted from tables so your queries perform better.

As with any other component of PostgreSQL, you can configure the autovacuum process to suit your business needs. This way, you can schedule it at times when there are fewer operations running on tables to make sure even the parallel execution has no impact on the tables.

DB admins can run the Vacuum command with any of its options at any given time on an ad hoc basis and on particular tables, if not on the entire database.

## Query Performance Tuning

It's important to understand how PostgreSQL executes our query on the data so that you can tune the query or the database itself to perform better. There are a few commands that help you automatically optimize query performance.

### ANALYZE

analyze is a command you can run on your databases and tables to measure some statistics about the tables. These calculated statistics are then stored in a PostgreSQL maintained table, which will then be used by the query planner to better plan query execution. When you submit a query, it's first transformed into PostgreSQL. This then goes to the planner, which comes up with a plan for the execution of the query.

The planner uses the stats of a table to form the execution plan. But with time, the stats become stale and the execution plan might not be accurate or performant. So you need to make sure the stats are always updated. This is where the analyze command comes in. When run, the command recalculates the stats for the given table and updates them in the database. This will then be used for planning the execution.

### EXPLAIN

explain is the logical next step to PostgreSQL query performance tuning after the analyze command. This outputs the plan that the database forms after using the stats calculated by the analyze command. This should give us a clear idea as to how the query is going to perform, if it's going to use an index scan or a table scan, etc. Based on this, we can either alter the query for better performance, or update the stats by running analyze again.

### Indexing Performance

Indexing is everything when it comes to query performance. We can see an immense difference in query performance with and without indexes. But this is not always the case. You need to understand how indexing can affect query performance based on a number of factors, such as the size of the table, how you are going to access the data in a table, what the indexing algorithm is, etc.

This requires a thorough understanding of all the use cases on a table.
Also, you need to note that whenever the data is updated in an indexed table, the indexes need to be updated as well, which adds overhead.

## PostgreSQL Performance Tuning Tools

Optimizing PostgreSQL performance depends on various factors that you should constantly keep an eye on. For this, you will need a proper monitoring tool that allows you to easily identify bottlenecks and decide on the proper actions. Such a solution helps you answer questions
like

- Do I need to add more resources on my physical server?

- Do I have some slow running queries that are affecting PostgreSQL
    > performance?

- Do I need more indexes to have a faster response on a frequently
    > performed query?

- Do I need to change my PostgreSQL configuration to limit the max
    > number of connections?

There are a variety of tools that can plug into a PostgreSQL database and collect metrics useful for troubleshooting. Each tool has its own areas of focus. Most can tap into not just the PostgreSQL database, but also the operating system. This helps in collecting metrics that can be looked at together and draw inferences about how system resources are affecting or are affected by query performance.

Most PostgreSQL monitoring solutions offer free or trial versions that you can set up easily to see what they offer and how they can help you [[monitor and optimize database
performance]](https://sematext.com/database-monitoring/). And they add little overhead to the normal functioning of a PostgreSQL database.

If you are looking for such a solution, check out this comparison of the [[best PostgreSQL monitoring tools]](https://sematext.com/blog/postgresql-monitoring/) available right now.

## Monitor and Tune PostgreSQL with Sematext

[[Sematext Cloud]](https://sematext.com/cloud/) is a full-stack monitoring solution with support for [[PostgreSQL monitoring and logging]](https://sematext.com/integrations/postgresql). It makes it easy to start monitoring PostgreSQL metrics without much hassle---you just need to install a lightweight agent on the machine and everything should be good to go. You get instant access to out-of-the-box monitoring dashboards, alerts and anomaly detection rules that you can then easily customize to fit your business needs.

Along with monitoring PostgreSQL itself, Sematext can also monitor any [[applications]](https://sematext.com/apm/) and [[containers]](https://sematext.com/container-monitoring/) that connect to PostgreSQL. Along with the ability to automatically capture logs from PostgreSQL, the tool makes it that much easier to spot problems with your database and entire environment. Read this article on [[troubleshooting PostgreSQL slow queries]](https://sematext.com/blog/postgresql-slow-queries/) to see how easy it is to use Sematext Cloud to speed up your database.

Sematext is capable of monitoring PostgreSQL databases no matter where they are hosted, on bare metal or the cloud with any cloud infrastructure provider. To explore [[Sematext Cloud]](https://sematext.com/cloud/) more closely and see what it has to offer, watch the video below or [[sign up]](https://apps.sematext.com/ui/registration) for a 14-day free trial.

