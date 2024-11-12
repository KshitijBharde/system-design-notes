## Function Requirements

- Pay-in flow: payment system receives money from customers on behalf of sellers.

- Pay-out flow: payment system sends money to sellers around the world.

## Non Functional Requirements

- Reliability and fault tolerance. Failed payments need to be carefully handled.

- A reconciliation process between internal services (payment systems, accounting systems) and external services (payment service providers) is required. The process asynchronously verifies that the payment information across these systems is consistent.

## Back-of-the-envelope estimation

- The system needs to process 1 million transactions per day, which is 10^6 / (24 * 3600) ~ 10 QPS.

- QPS is not high, the focus is on handling transactions correctly instead of providing high throughput.

