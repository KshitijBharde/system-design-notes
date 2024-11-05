### Scaling For Millions Of Users

- Relational databases are also called a relational database management system (RDBMS) or SQL database. The most popular ones are MySQL, Oracle database, PostgreSQL, etc. Relational databases represent and store data in tables and rows. You can perform join operations using SQL across different database tables.

- Non-Relational databases are also called NoSQL databases. Popular ones are CouchDB, Neo4j, Cassandra, HBase, Amazon DynamoDB, etc. These databases are grouped into four categories: key-value stores, graph stores, column stores, and document stores. Join operations are generally not supported in non-relational databases.

- Non-relational databases might be the right choice if:

	Your application requires super-low latency.

	Your data are unstructured, or you do not have any relational data.

	You only need to serialize and deserialize data (JSON, XML, YAML, etc.).

	You need to store a massive amount of data.


- Vertical scaling, referred to as “scale up”, means the process of adding more power (CPU, RAM, etc.) to your servers.

- Horizontal scaling, referred to as “scale-out”, allows you to scale by adding more servers into your pool of resources.

- When traffic is low, vertical scaling is a great option, and the simplicity of vertical scaling is its main advantage. Unfortunately, it comes with serious limitations.

	Vertical scaling has a hard limit. It is impossible to add unlimited CPU and memory to a single server.

	Vertical scaling does not have failover and redundancy. If one server goes down, the website/app goes down with it completely.

- A load balancer evenly distributes incoming traffic among web servers that are defined in a load-balanced set.

- For better security, private IPs are used for communication between servers.
	A private IP is an IP address reachable only between servers in the same network; however, it is unreachable over the internet. The load balancer communicates with web servers through private IPs.

- Database replication
	A master database generally only supports write operations. A slave database gets copies of the data from the master database and only supports read operations.

- Most applications require a much higher ratio of reads to writes; thus, the number of slave databases in a system is usually larger than the number of master databases.

- Advantages of database replication:

	Better performance: In the master-slave model, all writes and updates happen in master nodes; whereas, read operations are distributed across slave nodes. This model improves performance because it allows more queries to be processed in parallel.

	Reliability: If one of your database servers is destroyed by a natural disaster, such as a typhoon or an earthquake, data is still preserved. You do not need to worry about data loss because data is replicated across multiple locations.

	High availability: By replicating data across different locations, your website remains in operation even if a database is offline as you can access data stored in another database server.


- **Cache**
	
    A cache is a temporary storage area that stores the result of expensive responses or frequently accessed data in memory so that subsequent requests are served more quickly.

- After receiving a request, a web server first checks if the cache has the available response. If it has, it sends data back to the client. If not, it queries the database, stores the response in cache, and sends it back to the client. This caching strategy is called a read-through cache. 

- Decide when to use cache. Consider using cache when data is read frequently but modified infrequently.

- It is a good practice to implement an expiration policy. It is advisable not to make the expiration date too short as this will cause the system to reload data from the database too frequently. Meanwhile, it is advisable not to make the expiration date too long as the data can become stale.

- This involves keeping the data store and the cache in sync. Inconsistency can happen because data-modifying operations on the data store and cache are not in a single transaction. When scaling across multiple regions, maintaining consistency between the data store and cache is challenging.

- Multiple cache servers across different data centers are recommended to avoid SPOF. Another recommended approach is to overprovision the required memory by certain percentages. This provides a buffer as the memory usage increases.

- Once the cache is full, any requests to add items to the cache might cause existing items to be removed. This is called cache eviction. Least-recently-used (LRU) is the most popular cache eviction policy. Other eviction policies, such as the Least Frequently Used (LFU) or First in First Out (FIFO), can be adopted to satisfy different use cases.

- **Least Recently Used (LRU)**
    
    Concept: This policy removes the least recently used entry when the cache is full. It assumes that entries that haven’t been accessed for a long time are less likely to be used in the near future.
    
    How it works:
    Every time an entry is accessed, it is marked as recently used.
    When the cache is full, the oldest or least recently accessed entry is evicted.

- **Least Frequently Used (LFU)**
    
    Concept: This policy removes the least frequently used entry when the cache is full. It tracks how often each entry is accessed and assumes that less frequently accessed entries are less likely to be needed.

    How it works:
    The cache maintains a frequency count of how often each entry is accessed.
    When evicting, the entry with the lowest frequency count is removed.


- **First In, First Out (FIFO)**
	Concept: This policy removes the oldest entry when the cache is full. It works on the principle that the first added entry is the first to be evicted, regardless of how often or recently it was accessed.
		
    How it works:
    Entries are added to the cache in a queue-like structure.
    When eviction is needed, the oldest (first added) entry is removed.

- **Random Replacement (RR)**: Evicts a random entry. It’s simple but unpredictable, used in low-overhead systems.

- **Time-to-Live (TTL)**: Evicts entries after a fixed time, commonly used in DNS caching.

- **Content delivery network (CDN)**

    A CDN is a network of geographically dispersed servers used to deliver static content. CDN servers cache static content like images, videos, CSS, JavaScript files, etc.

    User A tries to get image.png by using an image URL. The URL’s domain is provided by the CDN provider.

     If the CDN server does not have image.png in the cache, the CDN server requests the file from the origin, which can be a web server or online storage like Amazon S3.

     The origin returns image.png to the CDN server, which includes optional HTTP header Time-to-Live (TTL) which describes how long the image is cached.

     The CDN caches the image and returns it to User A. The image remains cached in the CDN until the TTL expires.

     User B sends a request to get the same image. The image is returned from the cache as long as the TTL has not expired.

- Considerations while using CDN:

    Cost: CDNs are run by third-party providers, and you are charged for data transfers in and out of the CDN. Caching infrequently used assets provides no significant benefits so you should consider moving them out of the CDN.

    Setting an appropriate cache expiry

    You should consider how your website/application copes with CDN failure

- **Stateless web tier**

    A good practice is to store session data in the persistent storage such as relational database or NoSQL. Each web server in the cluster can access state data from databases. This is called stateless web tier.

    A stateful server remembers client data (state) from one request to the next. A stateless server keeps no state information.

    In this stateless architecture, HTTP requests from users can be sent to any web servers, which fetch state data from a shared data store. State data is stored in a shared data store and kept out of web servers. A stateless system is simpler, more robust, and scalable.

- **Data Centers**

    users are geoDNS-routed, also known as geo-routed, to the closest data center, with a split traffic of x% in US-East and (100 – x)% in US-West. geoDNS is a DNS service that allows domain names to be resolved to IP addresses based on the location of a user.

    Effective tools are needed to direct traffic to the correct data center. GeoDNS can be used to direct traffic to the nearest data center depending on where a user is located.

    Users from different regions could use different local databases or caches. In failover cases, traffic might be routed to a data center where data is unavailable. A common strategy is to replicate data across multiple data centers.

    With multi-data center setup, it is important to test your website/application at different locations. Automated deployment tools are vital to keep services consistent through all the data centers


- **Message Queues**

    A message queue is a durable component, stored in memory, that supports asynchronous communication. It serves as a buffer and distributes asynchronous requests.

    Decoupling makes the message queue a preferred architecture for building a scalable and reliable application. With the message queue, the producer can post a message to the queue when the consumer is unavailable to process it. The consumer can read messages from the queue even when the producer is unavailable.


- **Logging**

    Monitoring error logs is important because it helps to identify errors and problems in the system. You can monitor error logs at per server level or use tools to aggregate them to a centralized service for easy search and viewing.

- **Metrics**

    collecting different types of metrics help us to gain business insights and understand the health status of the system. Some of the following metrics are useful.

    Host level metrics: CPU, Memory, disk I/O, etc.

    Aggregated level metrics: for example, the performance of the entire database tier, cache tier, etc.

    Key business metrics: daily active users, retention, revenue, etc.

- **Automation**

    When a system gets big and complex, we need to build or leverage automation tools to improve productivity. Continuous integration is a good practice, in which each code check-in is verified through automation, allowing teams to detect problems early. Besides, automating your build, test, deploy process, etc. could improve developer productivity significantly.

- **Database scaling**

    **Vertical scaling**, also known as scaling up, is the scaling by adding more power (CPU, RAM, DISK, etc.) to an existing machine. There are some powerful database servers. According to Amazon Relational Database Service (RDS), you can get a database server with 24 TB of RAM. This kind of powerful database server could store and handle lots of data.

    You can add more CPU, RAM, etc. to your database server, but there are hardware limits. If you have a large user base, a single server is not enough.

    Greater risk of single point of failures.

    The overall cost of vertical scaling is high. Powerful servers are much more expensive.


    **Horizontal scaling**, also known as sharding, is the practice of adding more servers.

    Sharding separates large databases into smaller, more easily managed parts called shards. Each shard shares the same schema, though the actual data on each shard is unique to the shard.

    The most important factor to consider when implementing a sharding strategy is the choice of the sharding key. Sharding key (known as a partition key) consists of one or more columns that determine how data is distributed.
    When choosing a sharding key, one of the most important criteria is to choose a key that can evenly distributed data.

    Challenges with horizontal scaling:

    **Resharding data:** Resharding data is needed when 1) a single shard could no longer hold more data due to rapid growth. 2) Certain shards might experience shard exhaustion faster than others due to uneven data distribution. When shard exhaustion happens, it requires updating the sharding function and moving data around. Consistent hashing is a commonly used technique to solve this problem.

    **Celebrity problem:** This is also called a hotspot key problem. Excessive access to a specific shard could cause server overload. Imagine data for Katy Perry, Justin Bieber, and Lady Gaga all end up on the same shard. For social applications, that shard will be overwhelmed with read operations. To solve this problem, we may need to allocate a shard for each celebrity. Each shard might even require further partition.

    **Join and de-normalization:** Once a database has been sharded across multiple servers, it is hard to perform join operations across database shards. A common workaround is to de-normalize the database so that queries can be performed in a single table.