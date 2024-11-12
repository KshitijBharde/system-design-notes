# Consumers

Consumers read data from a topic(identified by name) - pull model.

Consumers request data from kafka brokers and get the response.

When consumer need to read data from a partition they will automatically know which broker server to read from.

In case of a broker failure, consumers know how to recover

Data is read in order from low to high offset within each partitions.

### Consumer Deserializer

It helps to transform bytes into objects

They are used on the values and the keys of the messages

**The serialization/deserialization type must not change during a topic lifecycle**

Consumer needs to know in advance what is expected format for your key and value in a topic

During a lifecyle of a topic you must not change the type of data being sent, this will break consumer.

## Consumer Groups

All the consumers in an application read data as a consumer group.

Each consumer within a group reads from exclusive paritions.

Example: Lets a say a topic has 5 partitions: 0, 1, 2, 3, 4.

And the consumer group for this topic has 3 consumers.

Now consumer 1 will read from parition 0, 1

consumer 2 from parition 2, 3

and consumer 4 from parition 4.

No two consumer in a group read from the same partition.

If you have more consumers than paritions, some consumers will be inactive.

### Mulitple consumer groups on one topic

It is acceptible to have multiple consumer group on the same topic

Example:

Lets take the example of truck_gps topic, there can be two or more applications that consumer the gps data namely: dashboard location service, notification service.

To support this case we can have two consumer groups: location_service_consumer_group & notification_service_consumer_group.

To create consumer group we use property group id to give a name to a group and the consumers will know in which group they belong.


## Consumer offsets

Kafka stores the offsets at which a consumer group has been reading

The offsets are committed in kafka topics name __consumer_offsets

When a consumer in a group has processed data received from kafka, it should be periodically committing the offsets, the kafka broker will write to __consumer_offsets.

If a consumer crashes, it will be able to read back from where it left off thanks to the committed offsets.

By default, Java Consumers will automatically commit the offsets in an atleast once mode.

There are 3 delivery semantics if you choose to do it manually

- At least once (usually preferred)

    Offsets are committed right after the message is processed
    
    If the processing goes wrong, the message will be read again.

    This can result in duplicate processing of messages. Make sure your processing idompotent(i.e. processing the message again doesn't affect your system)

- At most once

    Offsets are committed as soon as messages are recieved

    If the processing fails, some messages will be lost (they won't be read again).

- Exactly once

    We read from a topic and write back to the topic we use the transactional API



In Apache Kafka, consumers in a consumer group and brokers coordinate to manage partition assignment, handle rebalancing, and ensure fault tolerance. Here’s an overview of how each of these works:

### 1. How Consumers Know Which Broker to Connect To for Topic Partitions

When a consumer joins a consumer group and subscribes to a topic, Kafka brokers help the consumer identify the correct brokers for each partition as follows:

1. **Metadata Fetch**: Each consumer initially fetches metadata from the Kafka cluster, which includes information on topics, partitions, and their respective leaders (the broker responsible for each partition).
2. **Connecting to Partition Leaders**: After receiving this metadata, the consumer can directly connect to the brokers that are the leaders of the partitions it has been assigned to. This ensures that each consumer only interacts with the brokers that have the data it needs.

### 2. Who Assigns Partitions to Consumers

Kafka assigns partitions to consumers within a consumer group through a process managed by the **group coordinator** and **consumer group leader**:

1. **Group Coordinator**: Each consumer group has a designated group coordinator, which is a broker responsible for managing the group. The group coordinator keeps track of consumers and handles rebalancing.
2. **Consumer Group Leader**: When a consumer group is formed (or when a rebalance is triggered), one consumer acts as the consumer group leader. This leader is responsible for partition assignment.
3. **Partition Assignment**: The leader chooses a partition assignment strategy, often using algorithms like Range, Round Robin, or Sticky assignment. The leader communicates the assignment to the group coordinator, which in turn informs the other consumers.

### 3. Rebalancing When a New Consumer Is Added or Removed

Kafka performs rebalancing automatically whenever a change in the consumer group occurs (e.g., a consumer joins or leaves):

1. **Rebalance Trigger**: Any addition or removal of a consumer from a group triggers the group coordinator to initiate a rebalance.
2. **Redistribution of Partitions**: During rebalancing, partitions are redistributed among the consumers in the group according to the chosen assignment strategy. This ensures a roughly equal distribution of partitions and helps maintain optimal load balancing.
3. **Notification of Rebalance Completion**: Once rebalancing is complete, the group coordinator informs each consumer of their new partition assignments.

During rebalancing, consumers temporarily stop processing messages, which can cause a brief period of unavailability, but it resumes once the rebalance is complete.

### 4. Handling Broker Failures

Kafka handles broker failures in a resilient way:

1. **Leader Election**: Kafka maintains multiple replicas of each partition. If a broker hosting a partition leader fails, the Kafka controller (a special broker responsible for cluster metadata) triggers a leader election to promote one of the in-sync replicas (ISRs) as the new leader.
2. **Metadata Update for Consumers**: Once a new leader is elected, Kafka updates the cluster metadata to reflect the change. Consumers that are affected will re-fetch metadata to learn about the new leader broker and reconnect as needed.
3. **Minimizing Data Loss**: Kafka replicates data across brokers to minimize data loss. The replication factor and acknowledgment settings (e.g., `acks=all`) are configured to ensure data durability and reduce the impact of broker failures.

These mechanisms allow Kafka to maintain high availability, minimize consumer disruption, and provide data resiliency even when brokers fail.