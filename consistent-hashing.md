## Consistent Hashing

To achieve horizontal scaling, it is important to distribute requests/data efficiently and evenly across servers.
Consistent hashing is a commonly used technique to achieve this goal.

### The rehashing problem

If you have n cache servers, a common way to balance the load is to use the following hash method:

serverIndex = hash(key) % N, where N is the size of the server pool.

Example:

We have 4 servers and 8 string keys, to fetch the server where a key is stored, we perform the modular operation f(key) % 4. For instance, hash(key0) % 4 = 1 means a client must contact server 1 to fetch the cached data.

This approach works well when the size of the server pool is fixed, and the data distribution is even.

However, problems arise when new servers are added, or existing servers are removed.

For example, if server 1 goes offline, the size of the server pool becomes 3. Using the same hash function, we get the same hash value for a key. But applying modular operation gives us different server indexes because the number of servers is reduced by 1.

Most keys are redistributed, not just the ones originally stored in the offline server.

**Definition**:

Consistent hashing is a special kind of hashing such that when a hash table is re-sized and consistent hashing is used, only k/n keys need to be remapped on average, where k is the number of keys, and n is the number of slots.


Assume SHA-1 is used as the hash function f, and the output range of the hash function is: x0, x1, x2, x3, …, xn. In cryptography, SHA-1’s hash space goes from 0 to 2^160 - 1.

That means x0 corresponds to 0, xn corresponds to 2^160 – 1, and all the other hash values in the middle fall between 0 and 2^160 - 1.

Hash servers: Using the same hash function f, we map servers based on server IP or name onto the ring. 

Keys are hashed using the same function onto the ring.

To determine which server a key is stored on, we go clockwise from the key position on the ring until a server is found.

### Adding a server

Adding a new server will only require redistribution of a fraction of keys.

Lets say there were 4 servers originally ranging from server-0 ... server-3.

Originally key0 was mapped to server-0 based on the logic mentioned above.

Now server-4 is introduced and now it's the server next to key0 on has ring, that's why only key0 will have to be redistributed.

The other keys are not redistributed based on consistent hashing algorithm.

### Removing a server

Same logic applies as adding a server, only a fraction of keys which are affected gets reassigned to the next server available on the hash ring.

### Issues with the basic approach

It is impossible to keep the same size of partitions on the ring for all servers considering a server can be added or removed.

A partition is the hash space between adjacent servers.

It is possible that the size of the partitions on the ring assigned to each server is very small or fairly large.

Second, it is possible to have a non-uniform key distribution on the ring.

A technique called virtual nodes or replicas is used to solve these problems.

### Virtual nodes

A virtual node refers to the real node, and each server is represented by multiple virtual nodes on the ring.

With virtual nodes, each server is responsible for multiple partitions.

To find which server a key is stored on, we go clockwise from the key’s location and find the first virtual node encountered on the ring.

As the number of virtual nodes increases, the distribution of keys becomes more balanced. This is because the standard deviation gets smaller with more virtual nodes, leading to balanced data distribution. Standard deviation measures how data are spread out.

### Find affected keys

When a server is added or removed, a fraction of data needs to be redistributed. How can we find the affected range to redistribute the keys?

When a server is added in the ring, then move anticlockwise from the new server till a server is found, now you need to redistribute the keys between the new server and the server behind it.

E.g., server-4 is added between server-3 and server-0, then keys lying b/w server-3 and server-4 which are located on server-0 now needs to stored on server-4.

When a server is removed, then move anticlockwise from it until a server is found, now you need to redistribute the keys lying b/w them to the next server found in clockwise direction.




