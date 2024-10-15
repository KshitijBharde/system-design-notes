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

