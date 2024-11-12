# Producers

Producers write data to topics(which are made up of partitions).

So producers write data to kafka topic partitions.

Producers know to which partition to write to and which kafka broker has it.

In case of kafka broker failures producers will automatically recover.

We have load balancing because producers are going to send data across all partitions based on some mechanism and this why kafka scales.

## Message Keys

Producers can choose to send a key with the message(string, number, binary, etc...) (Optional).

If key=null, data is sent round robin(parition 0, then 1, then 2...) and this is how we get load balancing.

If key!=null, that means key has some value, and then all messages for that key will always go to the same partition(hashing).

This property is very important, A key are typically sent if you need message ordering for a specific field (e.g., truck_id).

Example:

Let's say you have topic in kafka to track location of trucks, the producers send the data(truck_id, location, timestamp) to the topic.

In order to get ordered location data for each truck we can use the truck_id_${id} as the key, this will make sure that the all the data regarding a particular truck is sent to only one partition and it would be ordered naturally.

### Components of a kafka message

- key (binary) (Optional: can be null)
- Value (binary) (Optinal: can be null)
- compression type (none, gzip, snappy, etc...)
- headers (key value pairs) (Optinal)
- parition + offset
- Timestamp (set by system or user)

## Kafka Message Serializer
- Kafka only accepts bytes as an input from producers and sends bytes out as an output to consumers
- Message serialization is transforming objects/data into bytes.
- They are used on the value and the key only
- Kafka producers come with common serializers like: String(including JSON), Int, Float, Avro, protobuf


## Kafka Message Key Hashing

- A Kafka partitioner is a code logic that takes a record and determines to which partition to send into.
- Key hashing is the process of determining the mapping of a key to a partition.
- In default the kafka partitioner they keys are hashed using murmur2 algorithm.

```
targetPartition = Math.abs(Utils.murmur2(keyBytes)) % (numPartitions - 1)
```

Important Note: **Producers are the ones who choose where the message is going to end up based on the key bytes.** 


## Producer Acknowledgements (acks)

Producers can choose to receive acks for data writes:

The confirmation from kafka broker that write actually happend

- acks=0 Producer wont wait for ack(possible data loss)
- acks=1 Producer will wait for leader ack(limited data loss)
- acks=all Leader + Replicas ack (no data loss)