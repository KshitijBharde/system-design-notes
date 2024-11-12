# Zookeeper

Zookeeper manages brokers (keeps a list of them)

Whenever a broker goes down it helps in  performing leader election for partitions

Zookeeper sends notifications to kafka in case of changes

(e.g., new topics, broker offline, broker back online, delete topics, etc...)

Kafka v2.x can't work without zookeeper

Kafka v3.x can work without zookeeper - using Kafka raft instead

Kafka v4.x will not have zookeeper

Zookeeper by design operates with odd number of servers (1, 3, 5, 7)

Zookeeper has a leader(writes) the rest of the servers are followers(reads)

Zookeeper does not store consumer offsets with kafka >v0.10

## Should you use Zookeeper?

**With Kafka brokers?**

Yes, until v4.0 is out while waiting for kafka without zookeeper to be production ready

**With Kafka Clients?**

Over time the clients and CLI have been migrated to leverage the brokers as a connection endpoint instead of zookeeper.

Since kafka v2.2 the kafka-topics.sh CLI command references to brokers and zookeeper for topic management (creation, deletion, etc...)
and the zookeeper CLI arguments are deprecated.

All the APIs and command are migrated to kafka instead, so that when clusters migrate without zookeeper, the change is invisible to the clients.

Zookeeper is less secure than kafka, and its port should be only opened for brokers and not kafka clients

**Never ever use Zookeeper as a configuration in your kafka clients, and other programs that connect to kafka**
