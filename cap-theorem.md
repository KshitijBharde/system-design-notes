# CAP Theorem

Desirable property of distributed system with replicated data.

The **CAP theorem** (also known as Brewer's theorem) is a fundamental principle in distributed systems that states:

A distributed database can only guarantee **two out of three** of the following properties at any given time:

1. **Consistency (C)**: Every read receives the most recent write or an error, meaning all nodes have the same data simultaneously.
2. **Availability (A)**: Every request (read or write) receives a response, even if some nodes are down.
3. **Partition Tolerance (P)**: The system continues to operate even if there is a network partition separating nodes.

### Why We Can’t Have All Three
The CAP theorem states that a system can only achieve **two out of these three properties** because of the inherent trade-offs. Specifically, **network partitions** (which are often unavoidable in distributed systems) force a choice between **consistency** and **availability**.

### Example: Distributed Data Storage During Network Partition

Imagine a distributed database with nodes in different data centers. The system aims to keep all nodes consistent, so every data write should update all nodes before any read can access the updated data.

#### Scenario:
- Suppose a user updates some data (e.g., changes their profile picture), and this update needs to propagate to all nodes.
- While this data update is in progress, a **network partition** occurs, splitting the nodes into two groups: **Group A** and **Group B**. These groups can no longer communicate with each other due to the partition.

#### Trade-Offs in the Face of the Partition:
1. **Consistency and Partition Tolerance (CP)**: 
   -  When a partition occurs between any two nodes, the system has to shut down the non-consistent node (i.e., make it unavailable) until the partition is resolved. In this case, if **Group A** receives the update, all the read request are redirected to it and the **Group B** is made unavailble until the partition is resolved and data is replicated.
   - This means the system **blocks all requests** to ensure consistency across the partition, sacrificing **availability**.
   - Result: **Consistent but unavailable** during the partition.

2. **Availability and Partition Tolerance (AP)**:
   - The system could choose to continue serving requests from both **Group A** and **Group B** independently.
   - However, without guaranteeing synchronization between the groups, **Group A** could serve outdated data if **Group B** received the update first, or vice versa.
   - This sacrifices **consistency** but allows the system to stay **available**.
   - Result: **Available but inconsistent** during the partition.

3. **Consistency and Availability (CA)**:
   - Theoretically, the system could try to stay both **consistent** and **available** by ensuring each node is up-to-date before serving requests.
   - However, to achieve this, **partition tolerance** would have to be sacrificed, meaning the system would fail entirely if a partition occurs.
   - Result: Only possible if the network is fully reliable (no partitions), which is unrealistic in most distributed systems.

### Conclusion
In distributed systems, network partitions are generally unavoidable. Therefore, a distributed system must choose between **consistency** and **availability** when a partition happens. This trade-off is why no distributed system can guarantee all three properties—**Consistency, Availability, and Partition Tolerance** simultaneously.