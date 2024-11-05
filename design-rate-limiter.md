## Design Rate Limiter

A rate limiter limits the number of client requests allowed to be sent over a specified period. If the API request count exceeds the threshold defined by the rate limiter, all the excess calls are blocked.


Benefits of using a rate limiter:

- Prevent resource starvation caused by Denial of Service (DoS) attack

- Reduce cost. Limiting excess requests means fewer servers and allocating more resources to high priority APIs. Rate limiting is extremely important for companies that use paid third party APIs.

- Prevent servers from being overloaded. To reduce server load, a rate limiter is used to filter out excess requests caused by bots or users’ misbehavior.


### Where to put the rate limiter?

- Client is an unreliable place to enforce rate limiting because client requests can easily be forged by malicious actors. Moreover, we might not have control over the client implementation.

- Server-side implementation.

- There is an alternative way, we create a rate limiter middleware(API Gateway), which throttles requests to your APIs.

- API gateway is a fully managed service that supports rate limiting, SSL termination, authentication, IP whitelisting, servicing static content, etc.


### Algorithms for Rate Limiting

**Token bucket**:

A token bucket is a container that has pre-defined capacity. Tokens are put in the bucket at preset rates periodically. Once the bucket is full, no more tokens are added.

The refiller puts 2 tokens into the bucket every second. Once the bucket is full, extra tokens will overflow.

Each request consumes one token. When a request arrives, we check if there are enough tokens in the bucket.

If there are enough tokens, we take one token out for each request, and the request goes through.

If there are not enough tokens, the request is dropped.


The token bucket algorithm takes two parameters:

Bucket size: the maximum number of tokens allowed in the bucket

Refill rate: number of tokens put into the bucket every second


How many buckets do we need? This varies, and it depends on the rate-limiting rules. Here are a few examples.

It is usually necessary to have different buckets for different API endpoints. For instance, if a user is allowed to make 1 post per second, add 150 friends per day, and like 5 posts per second, 3 buckets are required for each user.

If we need to throttle requests based on IP addresses, each IP address requires a bucket.

If the system allows a maximum of 10,000 requests per second, it makes sense to have a global bucket shared by all requests.

Pros:

The algorithm is easy to implement.

Memory efficient.

Token bucket allows a burst of traffic for short periods. A request can go through as long as there are tokens left.

Cons:

Two parameters in the algorithm are bucket size and token refill rate. However, it might be challenging to tune them properly.

**Leaking bucket**:

The leaking bucket algorithm is similar to the token bucket except that requests are processed at a fixed rate.

It is usually implemented with a first-in-first-out (FIFO) queue. The algorithm works as follows:

When a request arrives, the system checks if the queue is full. If it is not full, the request is added to the queue.

Otherwise, the request is dropped.

Requests are pulled from the queue and processed at regular intervals.

Leaking bucket algorithm takes the following two parameters:

Bucket size: it is equal to the queue size. The queue holds the requests to be processed at a fixed rate.

Outflow rate: it defines how many requests can be processed at a fixed rate, usually in seconds.

Shopify, an ecommerce company, uses leaky buckets for rate-limiting [7].

Pros:

Memory efficient given the limited queue size.

Requests are processed at a fixed rate therefore it is suitable for use cases that a stable outflow rate is needed.

Cons:

A burst of traffic fills up the queue with old requests, and if they are not processed in time, recent requests will be rate limited.

There are two parameters in the algorithm. It might not be easy to tune them properly.


**Fixed window counter**

Fixed window counter algorithm works as follows:

The algorithm divides the timeline into fix-sized time windows and assign a counter for each window.

Each request increments the counter by one.

Once the counter reaches the pre-defined threshold, new requests are dropped until a new time window starts.

A major problem with this algorithm is that a burst of traffic at the edges of time windows could cause more requests than allowed quota to go through. Consider the following case:

the system allows a maximum of 5 requests per minute, and the available quota resets at the human-friendly round minute. As seen, there are five requests between 2:00:00 and 2:01:00 and five more requests between 2:01:00 and 2:02:00. For the one-minute window between 2:00:30 and 2:01:30, 10 requests go through. That is twice as many as allowed requests.

Pros:

Memory efficient.

Easy to understand.

Resetting available quota at the end of a unit time window fits certain use cases.

Cons:

Spike in traffic at the edges of a window could cause more requests than the allowed quota to go through.

**Sliding window log**

As discussed previously, the fixed window counter algorithm has a major issue: it allows more requests to go through at the edges of a window. The sliding window log algorithm fixes the issue. It works as follows:

The algorithm keeps track of request timestamps. Timestamp data is usually kept in cache, such as sorted sets of Redis [8].

When a new request comes in, remove all the outdated timestamps. Outdated timestamps are defined as those older than the start of the current time window.

Add timestamp of the new request to the log.

If the log size is the same or lower than the allowed count, a request is accepted. Otherwise, it is rejected.

In this example, the rate limiter allows 2 requests per minute. Usually, Linux timestamps are stored in the log. However, human-readable representation of time is used in our example for better readability.

The log is empty when a new request arrives at 1:00:01. Thus, the request is allowed.

A new request arrives at 1:00:30, the timestamp 1:00:30 is inserted into the log. After the insertion, the log size is 2, not larger than the allowed count. Thus, the request is allowed.

A new request arrives at 1:00:50, and the timestamp is inserted into the log. After the insertion, the log size is 3, larger than the allowed size 2. Therefore, this request is rejected even though the timestamp remains in the log.

A new request arrives at 1:01:40. Requests in the range [1:00:40,1:01:40) are within the latest time frame, but requests sent before 1:00:40 are outdated. Two outdated timestamps, 1:00:01 and 1:00:30, are removed from the log. After the remove operation, the log size becomes 2; therefore, the request is accepted.

Pros:

Rate limiting implemented by this algorithm is very accurate. In any rolling window, requests will not exceed the rate limit.
Cons:

The algorithm consumes a lot of memory because even if a request is rejected, its timestamp might still be stored in memory.


**Sliding window counter**

The sliding window counter algorithm is a hybrid approach that combines the fixed window counter and sliding window log.

Assume the rate limiter allows a maximum of 7 requests per minute, and there are 5 requests in the previous minute and 3 in the current minute. For a new request that arrives at a 30% position in the current minute, the number of requests in the rolling window is calculated using the following formula:

Requests in current window + requests in the previous window * overlap percentage of the rolling window and previous window

Using this formula, we get 3 + 5 * 0.7% = 6.5 request. Depending on the use case, the number can either be rounded up or down. In our example, it is rounded down to 6.

Since the rate limiter allows a maximum of 7 requests per minute, the current request can go through. However, the limit will be reached after receiving one more request.

Pros

It smooths out spikes in traffic because the rate is based on the average rate of the previous window.

Memory efficient.

Cons

It only works for not-so-strict look back window. It is an approximation of the actual rate because it assumes requests in the previous window are evenly distributed. However, this problem may not be as bad as it seems. According to experiments done by Cloudflare [10], only 0.003% of requests are wrongly allowed or rate limited among 400 million requests.

### Where shall we store counters?

Using the database is not a good idea due to slowness of disk access. In-memory cache is chosen because it is fast and supports time-based expiration strategy. For instance, Redis is a popular option to implement rate limiting. It is an in-memory store that offers two commands: INCR and EXPIRE.

### Exceeding the rate limit

In case a request is rate limited, APIs return a HTTP response code 429 (too many requests) to the client.

Depending on the use cases, we may enqueue the rate-limited requests to be processed later. For example, if some orders are rate limited due to system overload, we may keep those orders to be processed later.


### Rate limiter headers

How does a client know whether it is being throttled? And how does a client know the number of allowed remaining requests before being throttled? The answer lies in HTTP response headers. 

X-Ratelimit-Remaining: The remaining number of allowed requests within the window.

X-Ratelimit-Limit: It indicates how many calls the client can make per time window.

X-Ratelimit-Retry-After: The number of seconds to wait until you can make a request again without being throttled.


### Rate limiter in a distributed environment

scaling the system to support multiple servers and concurrent threads is a different story. There are two challenges:

Race condition:

Locks are the most obvious solution for solving race condition. However, locks will significantly slow down the system. Two strategies are commonly used to solve the problem: Lua script and sorted sets data structure in Redis.

Synchronization issue:

One possible solution is to use sticky sessions that allow a client to send traffic to the same rate limiter. This solution is not advisable because it is neither scalable nor flexible. A better approach is to use centralized data stores like Redis.

### Performance optimization

First, multi-data center setup is crucial for a rate limiter because latency is high for users located far away from the data center. Most cloud service providers build many edge server locations around the world.

Traffic is automatically routed to the closest edge server to reduce latency.

Second, synchronize data with an eventual consistency model.

### Monitoring

After the rate limiter is put in place, it is important to gather analytics data to check whether the rate limiter is effective. Primarily, we want to make sure:

The rate limiting algorithm is effective.

The rate limiting rules are effective.

if rate limiting rules are too strict, many valid requests are dropped. In this case, we want to relax the rules a little bit. In another example, we notice our rate limiter becomes ineffective when there is a sudden increase in traffic like flash sales. In this scenario, we may replace the algorithm to support burst traffic. Token bucket is a good fit here.


### Reference

https://engineering.classdojo.com/blog/2015/02/06/rolling-rate-limiter/


The algorithm explained in the article leverages Redis sorted sets to implement a more sophisticated rate-limiting mechanism that avoids race conditions, allows for atomic operations, and provides flexibility in setting multiple rate limits. Here’s a breakdown of how it works and why it’s beneficial compared to traditional token bucket algorithms.

1. Limitations of the Basic Token Bucket
In a token bucket algorithm:

Each user has a "bucket" with tokens representing allowed actions.
Tokens are consumed when an action is performed and refilled over time up to a maximum capacity.
The challenge with a basic implementation is that refilling tokens for each user requires continuous updates, which can create unsustainable load on Redis, especially with millions of users. In addition, if two processes check and update the token count at the same time, they can cause race conditions, potentially allowing more actions than the limit.

2. Using Sorted Sets for Rate Limiting
Redis sorted sets can solve these issues by tracking timestamps of each action and enabling atomic operations. The sorted set (ZSET) data structure allows each action timestamp to be stored in a sorted, time-ordered manner, making it easier to check recent actions within a time window without needing to continuously refill tokens. Here’s how it works:

Step-by-Step Breakdown of the Algorithm
Each User Has a Sorted Set:

For each user, a sorted set is created.
In the sorted set, each element represents the timestamp of an action attempt.
The score and the value for each element are set to the timestamp of the action (in microseconds).
Clear Old Entries:

Before evaluating if a new action is allowed, the algorithm removes any elements that fall outside the rate-limiting window (e.g., actions that occurred more than a minute ago for a 1-minute rate limit).
This is done using ZREMRANGEBYSCORE, which deletes all elements with timestamps older than the allowed time interval.
Fetch the Number of Remaining Actions:

After removing outdated entries, the current set is fetched using ZRANGE to get a list of all recent actions within the allowed window.
The number of elements in the set represents the number of actions performed in the current rate-limiting interval.
Add the New Action:

The current timestamp is added to the sorted set using ZADD, marking the new action attempt.
This ensures that any subsequent actions have the latest information about the user’s recent activity.
Count Actions to Decide Allowance:

After adding the current action, the algorithm counts the elements in the sorted set. If the count exceeds the rate limit, the action is blocked.
Optionally, the algorithm can also check the difference between the most recent action timestamp and the current timestamp to ensure actions aren’t too close together.
Use TTL to Manage Memory:

To save memory, a TTL (time-to-live) is set on the sorted set, equal to the rate-limiting interval. This automatically deletes the set if it becomes inactive for longer than the rate-limiting interval.
Example Flow in a 10-Requests-Per-Minute Rate Limiter
For a rate limit of 10 actions per minute:

The sorted set contains timestamps for each action within the last minute.
If a new action is attempted:
The algorithm removes all timestamps older than 1 minute.
Counts the remaining actions in the sorted set.
If there are fewer than 10 actions, it adds the current timestamp and allows the action.
If there are 10 or more actions, the action is blocked.
Advantages of Using Sorted Sets for Rate Limiting
Atomicity with Redis MULTI:

All operations (removing old actions, checking the count, adding a new action) can be wrapped in a Redis MULTI transaction, making them atomic. This avoids race conditions, ensuring that no two processes get inconsistent views of the user’s recent actions.
Precision in Time Windows:

This method allows for fine-grained control, such as checking for both a per-minute and a per-second rate limit in one set.
For example, you could enforce limits like "no more than 10 actions per minute and no more than 2 actions every 3 seconds."
No Continuous Refills Needed:

Unlike token bucket implementations that require regular token refill operations, this approach doesn’t need continuous updates. Instead, it simply removes outdated entries in real-time when a new action is attempted.
Caveats and Considerations
Blocked Actions Still Count: If a user exceeds the rate limit, each blocked action still counts as an entry in the sorted set. This means that if they repeatedly try to exceed the rate limit, it will continuously block them without allowing intermittent successful actions. This design works for most cases but may need adjustments if occasional actions are desired even after blocks.

Memory Usage: Each action takes up an entry in the sorted set, so high-traffic users could have larger sets. The TTL helps manage memory by removing inactive sets, but for very high action rates, Redis memory usage should be monitored.

Final Thoughts
Using Redis sorted sets for rate limiting is a powerful approach, especially in distributed systems where you need atomic, race-condition-free enforcement of rate limits. The approach effectively balances memory use, simplicity, and precision without the need for regular token refills, making it scalable for millions of users.
