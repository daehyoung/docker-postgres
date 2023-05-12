
## the CAP theorem
- Consistency: This term indicates whether all the nodes in a cluster see the same data at the same time or not. A read-only node has to see all previously completed reads at any time.
- Availability: Reads and writes have to succeed all the time. In other words a node has to be available for users at any point of time.
- Partition tolerance: This means that the system will continue to work even if arbitrary messages are lost on the way. A network partition event occurs when a system is no longer accessible (think of a network connection failure).
A different way of considering partition tolerance is to think of it as message passing. If an individual system can no longer send or receive messages from other systems, it means that it has been effectively partitioned out of the network. The guaranteed properties are maintained even when network failures prevent some machines from communicating with others.


a replicated (or distributed) system can provide only two out of these three features at the same time.


It is theoretically impossible to offer consistency, availability, and partition tolerance
at the same time.


PostgreSQL, Oracle, DB2, and so on will provide you with CAp("consistent" and "available"), while NoSQL systems, such as MongoDB and Cassandra, will provide you with cAP ("available" and "partition tolerant"). This is why NoSQL is often referred to as eventually consistent.
