# Topic Replication Factor

Topics should have a replication factor > 1 (usually between 2 and 3)

This way a broker is down, another broker can serve the data

Example: Topic-A with 2 partitions and replication factor of 2

There are 3 brokers available:

Broker 101 - Partition-0 Topic A

Broker 102 - Partition-1 Topic A 
             Replica of Partition-0 Topic A

Broker 103 - Replica of Partition-1 Topic A


If broker 102 crashes then we still have 101 and 102 to receive and send data.


## Concept of leader for a partition

At any time only one broker can be a leader for a given partition

Producers can only send data to the broker that is leader of a partition

The other brokers will replicate the data

Each partition has one leader and multiple ISR(In-sync replica).

Consumers by default will read from the leader broker of partition


Since Kafka v2.4, it is possible to configure consumer to read from the closes replica

This may help reduce latency, also decrease network costs if using the cloud