
# Synchronous versus asynchronous replication

```
BEGIN;
INSERT INTO foo VALUES ('bar');
COMMIT;
```

### Asynchronous replication
- the data can be replicated after the transaction has been committed on the master. 
- the slave is never ahead of the master; and in the case of writing, it is usually a little behind the master.
- This delay is called lag.

### Synchronous replication 
- enforces higher rules of consistency. 
- the system has to ensure that the data written by the transaction will be at least on two servers at the time the transaction commits.
- the slave does not lag behind the master 
- the data seen by the end users will be identical on both the servers.
- this will lead to latency issues.
- In the case of synchronous replication, confirmations from a remote server are needed. This, of course, causes some additional overhead.
- Use synchronous replication only when it is really needed.

### Understanding replication and data loss

Let's assume that we are replicating data asynchronously in the following manner:
1. A transaction is sent to the master.
2. It commits on the master.
3. The master dies before the commit is sent to the slave.
4. The slave will never get this transaction.

If data can be lost, we are about to sacrifice the consistency part of CAP.

If you want to make sure that data can never be lost, you have to switch to synchronous replication.

# Single-master versus multimaster replication

### Single-master
- writes can go to exactly one server
- distributes the data to the slaves inside the setup. 
- Slaves may receive only reads, and no writes.

### multi-master 
- replication allows writes to all the servers inside a cluster. 
- multimaster replication adds a lot of complexity to the system. 
- writes can go to many nodes at the same time
- the cluster has to be perfectly aware of conflicts and handle them gracefully.


# Logical versus physical replication

### Physical replication 
- the system will move data as is to the remote box. 
- So, if something is inserted, the remote box will get data in binary format, not via SQL.
### Logical replication 
- a change, which is equivalent to data coming in, is replicated.

### example
```
test=# CREATE TABLE t_test (t date);
CREATE TABLE
test=# INSERT INTO t_test VALUES (now())
RETURNING *;
t
------------
2013-02-08
(1 row)
INSERT 0 1
```

#### Logical replication

```
INSERT INTO t_test VALUES ('2013-02-08');
```
- function call has been replaced with the real value.
- the content should be identical, but it makes no difference whether it is in the same place or not.
- Logical replication is usually a little harder to set up
- it offers greater flexibility.
- not bound to any specific version of PostgreSQL,

#### Physical replication

1. Added an 8 K block to pg_class and put a new record there (to indicate
that the table is present).
2. Added rows to pg_attribute to store the column names.
3. Performed various changes inside the indexes on those tables.
4. Recorded the commit status, and so on.

- create a copy of your system that is (largely) identical on the physical level. 
- the same data will be in the same place inside your tables on all boxes. 
- very convenient to use and especially easy to set up. 
- It is widely used when the goal is to have identical replicas of your system (to have a backup or to simply scale up).
- rely on the fact that every version of PostgreSQL has the same on-disk layout.
