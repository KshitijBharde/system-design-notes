# Introduction

### Why we need kafka?

For gathering data from different source systems usually we start off by writing an integration between source and target system.
After some time the no. of source and target system increases and we are writing integration for each one of the individually, this is difficult scale, maintain, debug.

For 4 sources and 6 target systems we need to write 24 integrations.

Each integration comes with its set of challenges like:

- Protocol - how is data transported(TCP, HTTP, REST, FTP, gRPC...)
- Data Format - How is data parsed(Binary, JSON, CSV< Avro, Protobuf...)
- Data Schema - How data is shaped and how it may evolve
- Added load of connections on source systems

### How apache kafka solves this?

We still have multiple source and target systems and in middle sits kafka.

The source systems are responsible for sending(producing) data to kafka.

Kafka has data streams for all your source systems and the target systems consume these streams of data.

Example of sources: website events, pricing data, financial transactions, user interaction, logs..

Example of target systems: databases, analytics, email systems, audit

### Why use kafka?

- open source project, created by linkedin
- Distributed, resilient architecture, failure tolerant
- Horizontal scalability:
    You can add brokers to the cluster over time.

    Can scale to 100s of brokers.

    Have huge scale of througput, can scale to millions of messages per sec 
- High performance(latency of less than 10ms) - real time
- Has been widely adopted

### Use cases

- Messaging system
- Activity tracking
- Gather metrics from many different sources
- Application logs gathering
- Stream processing
- De-coupling of system dependencies
- Has integration with spark, flink, storm, hadoop and other big data technologies
- microservice pub/sub