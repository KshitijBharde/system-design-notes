# Kafka KRaft

In 2020 kafka team started to remove the zookeeper dependency.

Zookeeper shows scaling issues when kafka cluster have > 100,000 partitions

By removing zookeeper, apache kafka can:

- Scale to millions of partitions and becomes easier to maintain and set-up
- Improve stability, makes it easier to monitor, support and administer
- Single security model for whole system, now that you have deal with only kafka security and not zookeeper security
- Single process to start with kafka
- Gives faster controller shutdown and recovery time

Kafka v3.x implements the Raft protocol (KRaft)

Production ready since v3.3.1

Kafka v.4.x will be released only with KRaft