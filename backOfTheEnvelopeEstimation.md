### Power of Two

To obtain correct calculations, it is critical to know the data volume unit using the power of 2.

A byte is a sequence of 8 bits. An ASCII character uses one byte of memory (8 bits).

- 2^10 ~ 1 thousand ~ 1 KB
- 2^20 ~ 1 million (1000 * 1000 | 10 lacs) ~ 1 MB
- 2^30 ~ 1 Billion (1000 million | 100 crores) ~ 1GB
- 2^40 ~ 1 Trillion(1000 billions) ~ 1TB
- 2^50 ~ 1 Quadrillion(1000 Trillions) ~ 1PB


### Important latency numbers

L2 cache reference - 7ns

Mutex lock/unlock - 100ns

Main memory reference - 100ns

Disk seek - 10ms

Read 1 MB sequentially from the network - 10ms

Read 1 MB sequentially from disk - 30ms

**Summary:**

    Memory is fast but the disk is slow.

    Avoid disk seeks if possible.

    Simple compression algorithms are fast.

    Compress data before sending it over the internet if possible.

    Data centers are usually in different regions, and it takes time to send data between them.

### Availability numbers

High availability is the ability of a system to be continuously operational for a desirably long period of time. High availability is measured as a percentage, with 100% means a service that has 0 downtime. Most services fall between 99% and 100%.

- 99%, Downtime per day = 14.40 min
- 99.99% Downtime per day = 864 sec


### Estimate Twitter QPS and storage requirements


Assumptions:

    300 million monthly active users.

    50% of users use Twitter daily.

    Users post 2 tweets per day on average.

    10% of tweets contain media.

    Data is stored for 5 years.


Estimations:

    Query per second (QPS) estimate:

    Daily active users (DAU) = 300 million * 50% = 150 million

    Tweets QPS = 150 million * 2 tweets / 24 hour / 3600 seconds = ~3500

    Peek QPS = 2 * QPS = ~7000


We will only estimate media storage here.

    Average tweet size:

    tweet_id 64 bytes

    text 140 bytes

    media 1 MB

    Media storage: 150 million * 2 * 10% * 1 MB = 30 TB per day

    5-year media storage: 30 TB * 365 * 5 = ~55 PB


Commonly asked back-of-the-envelope estimations: QPS, peak QPS, storage, cache, number of servers, etc. You can practice these calculations when preparing for an interview. Practice makes perfect.