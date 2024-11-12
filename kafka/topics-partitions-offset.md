## Topics

Topics are particular stream of data within your kafka cluster, in a cluster you could have many topics like: logs, purchases, twitter_tweets, trucks_gps.
 
Topic would be similar to a table in a database but without any constraint, because you send whatever you want to a kafka topic without any data validation.

You can have as many topics as you want in a cluster.

A topic is identified by its name in the cluster.

These topics can support any kind of data format e.g., json, binary, protobuff, text files etc.

The sequence of messages in a topic is called data stream.

You cannot query topis, so they are similar to tables in a database but you cannot query them.

To add data to kafka topics use producers and to read data from topic we use consumers.

## Partitions

Topics are split into partitions(example: 100 partitions).
 
Messages sent to a topic ends up in these partitions, messages within each partition are ordered.

The messages are ordered by IDs, each message is assigned a partition in ever increasing manner.

This ID is called kafka partition offset.

These offset IDs are unique in a particular partition not across all partitions of a topic.

A message can be referrenced using the partition and offset e.g., partition-01 offset-101.

Kafka topics are immutable, once a data is written to a partition it cannot be changed. You cannot delete or update data in partition.


## Important Notes

- Once data is written to a partition, it cannot be changed(immutability).
- Data is kept only for a limited time(default is one week - configurable).
- Offset only has meaning for a specific partition. Offsets are repeated across partitions.

    e.g., Offset 3 in partition 0 doesn't represent the same data as offset 3 in partition 1.

    Offsets are not re-used even if previous messages have been deleted.

- Order is guaranteed only within a partition(not across partitions).
- Data is assigned randomly to a partition unless a key is provided.
- You can have as many partitions per topic as you want.


