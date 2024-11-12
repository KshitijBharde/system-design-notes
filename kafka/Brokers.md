# Brokers

A Kafka cluster is composed of multiple borkers(servers).

The servers are called brokers because they receive and send data.

Each broker is identified with its ID(integer).

Each broker contains certain topic partitions.

After connecting to a bootstrap broker, you will be connected to the entire cluster.

A good number to get started is 3 brokers, but some big clusters have over 100 brokers.

Paritions are distributed equally among available brokers.

No Broker have all the data, the broker only have the data that they should have/assigned.

## Kafka Broker Discovery

Evert kafka broker is also called as a "bootstrap server".

That means that you only need to connect to one broker, and the Kafka clients will know how to be connected to the entire cluster.

Steps:

- Kafka client initiates a connection with the bootstrap server also requests metadata.

- bootstrap server returns the list of all brokers in the cluster and more data as well like which broker has which partition

- Now the client is able to connect to the broker it needs to produce or consume data.

Each broker knows about all brokers, topics and partitions (metadata)
