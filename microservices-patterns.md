# Microservices Patterns

## Decomposition Pattern

In microservices architecture, **decomposition by business capability** and **decomposition by subdomain** are two common strategies for breaking down a system into manageable, well-defined services.

Both approaches aim to divide an application in ways that align closely with the business, but they do so from slightly different perspectives.

### 1. Decomposition by Business Capability

- **Definition**: This approach breaks down a system based on the organization’s core business capabilities or functions. A business capability is a specific area of business functionality that delivers value to customers and is usually stable over time. Examples include **billing**, **customer management**, **inventory**, and **order processing**.
  
- **How it Works**: Each microservice is responsible for a single business capability and contains all of the necessary components (UI, business logic, data access) related to that capability. This encapsulation allows services to operate independently and makes them less prone to affect other parts of the system.

- **Benefits**:
  - **Clear Ownership**: Teams can own specific business capabilities, allowing for better focus, accountability, and expertise in that area.
  - **Alignment with Business Logic**: Each service reflects a core part of the business, making it easier to align development with business goals and enabling faster, independent updates to specific areas.

- **Example**: In an e-commerce platform, separate services could be created for **Catalog Management**, **Order Processing**, **Customer Service**, and **Payment Handling**.

### 2. Decomposition by Subdomain

- **Definition**: This approach is based on **Domain-Driven Design (DDD)** principles and focuses on breaking down the system into subdomains within the overall business domain. Subdomains are the different problem areas within the business domain that contribute to the functionality of the system. Each subdomain can be **Core**, **Supporting**, or **Generic**.
  
  - **Core Subdomain**: Provides the primary value of the business and is unique to it. Example: Payment processing for a fintech app.
  - **Supporting Subdomain**: Necessary for the business but not core. Example: Reporting and analytics.
  - **Generic Subdomain**: Common functionalities that could be used by many businesses. Example: Authentication and authorization.

- **How it Works**: Each microservice corresponds to a subdomain and encapsulates the functionality and data related to that specific subdomain. This approach aligns services with the problem areas identified by DDD, making it easier to manage complexity and model the domain accurately.

- **Benefits**:
  - **Improved Domain Modeling**: Subdomain-based decomposition helps teams create services that reflect the natural boundaries within the business domain, leading to a more accurate and manageable architecture.
  - **Focus on Business Logic**: By isolating subdomains, teams can concentrate on the specific rules and logic that pertain to each area, which can lead to better domain-driven design and problem-solving.

- **Example**: In a healthcare system, subdomains could include **Patient Management**, **Billing**, **Scheduling**, and **Clinical Records**.

### Comparison

| Pattern                         | Decomposition by Business Capability | Decomposition by Subdomain     |
|---------------------------------|--------------------------------------|--------------------------------|
| **Basis of Decomposition**      | Business capabilities (functional areas) | Business subdomains (DDD principles) |
| **Purpose**                     | Directly aligns with the organization’s functional structure | Aligns with specific problem areas within the business domain |
| **Focus**                       | Ownership of complete business functionality | Accurate domain modeling through subdomains |
| **Ideal For**                   | Systems where functional areas are clearly defined | Systems with complex domains that benefit from DDD |

In practice, both decomposition approaches can be combined. For example, a business capability (like **Order Management**) can be further broken down into subdomains (like **Order Creation**, **Order Fulfillment**, and **Order Tracking**) to create a more granular, organized structure that aligns closely with both business needs and domain logic.

## Strangler Pattern

The **Strangler Pattern** is a design pattern commonly used in **microservices** and **legacy system modernization**. It refers to the process of gradually replacing or refactoring an old system with a new one, without needing to completely rewrite the entire system from scratch. The term “strangler” comes from the metaphor of a vine strangling a tree, slowly taking over its structure while the tree still functions.

### **How It Works**
1. **Initial State**: The old system (often monolithic) is running and handling all business functions.
2. **Incremental Replacement**: A new system (often microservices-based) is built alongside the old system, and the two systems begin to coexist. Instead of doing a complete "big bang" rewrite, functionality is migrated incrementally.
3. **Routing Requests**: New requests are gradually routed to the new system while the old system is still handling its part. Over time, more parts of the old system are replaced, and eventually, the old system is "strangled" and removed.
4. **Decommissioning**: Once all functionality has been migrated, the old system is no longer in use, and it can be decommissioned.

---

### **Benefits of the Strangler Pattern**

1. **Reduced Risk**:
   - By migrating incrementally, the risks associated with rewriting the entire system at once are mitigated. The business continues to operate while the transition happens step by step.
   - Issues or bugs can be detected early during the migration and fixed in isolation.

2. **Continuous Delivery**:
   - The business can continue operating with the legacy system while the new system is developed. This approach ensures that there is no downtime during the transition.
   - New functionality can be added to the new system without disrupting the ongoing operation of the old system.

3. **Minimal Disruption**:
   - The migration does not require a full stop or complete disruption. As each piece of functionality is replaced, the old and new systems can work together in parallel.
   
4. **Iterative and Flexible**:
   - The strangler pattern allows for iterative development. Teams can prioritize which parts of the old system to migrate based on business needs, rather than committing to a massive migration at once.

---

### **Challenges of the Strangler Pattern**

1. **Complexity in Integration**:
   - Running two systems in parallel can introduce complexities, especially around data consistency, transaction management, and ensuring that the two systems communicate seamlessly.
   
2. **Technical Debt**:
   - During the migration period, the organization may be left managing two systems that are loosely coupled but still interdependent. This could lead to increased maintenance overhead.
   
3. **Longer Migration Time**:
   - Depending on the size of the legacy system, the strangler pattern can take a long time to fully migrate to the new system, which might delay the benefits of the new architecture.

---

### **Use Cases for the Strangler Pattern**

- **Legacy System Modernization**: Often used when transitioning from monolithic or outdated systems to modern, microservices-based architectures without the need for an immediate, full rewrite.
- **Incremental Refactoring**: When parts of a system need to be refactored to meet new requirements, the strangler pattern allows for gradual and controlled changes.
- **Migration to Cloud**: Organizations moving from on-premise infrastructure to the cloud can use this pattern to replace old services with cloud-native applications without disrupting current business operations.

---

### **In Summary**
The **Strangler Pattern** allows an organization to migrate from a legacy system to a new architecture incrementally and safely. It reduces risk, ensures business continuity, and makes it easier to modernize a system over time. However, it requires careful management of integration and coordination between the old and new systems during the transition period.



## **Shared Database**

In a shared database pattern, multiple microservices access a common database. Instead of each service managing its own database, they interact with a single database that holds the data for all services.

- **Pros**:
  - **Simplified Data Consistency**: All services share the same database, so maintaining consistency across services can be simpler.
  - **Easier Data Access**: Services can read or modify data that belongs to other services without needing complex API calls or data replication.
  - **Less Complexity**: Reduces the overhead of managing multiple databases, backups, and data stores.

- **Cons**:
  - **Tight Coupling**: Services become tightly coupled, as changes to the database schema can impact multiple services. This can reduce the flexibility to change or evolve individual services.
  - **Scalability Bottleneck**: A single database can become a bottleneck as the number of services and database access increases, limiting scalability.
  - **Potential for Cross-Service Errors**: A failure or heavy query load from one service can impact the performance or availability of the database for other services.
  - **Limited Autonomy**: Services lose independence, as they’re bound to a shared database schema, which restricts their ability to evolve separately.

---

## **Database per Service**

In this pattern, each microservice has its own dedicated database. This allows each service to manage its data independently and ensures clear ownership of data.

- **Pros**:
  - **Service Independence**: Each service can modify its own database without impacting others, leading to a more modular architecture.
  - **Improved Scalability**: Services can scale independently, with dedicated databases that can be optimized and scaled separately.
  - **Enhanced Fault Isolation**: Database failures or heavy load in one service do not impact the databases of other services.

- **Cons**:
  - **Data Consistency Challenges**: Maintaining data consistency across services can be complex, as each service has its own database, requiring additional coordination (e.g., using the Saga pattern).
  - **Increased Operational Complexity**: Managing multiple databases adds to the operational overhead, including backup, monitoring, and maintenance.
  - **Cross-Service Data Access Complexity**: Accessing data owned by another service requires inter-service communication (e.g., APIs), which can add latency and complexity.

---

### 3. **Saga Pattern**

The Saga pattern is used for managing distributed transactions across multiple microservices. Instead of a single, atomic transaction, a saga breaks it down into a series of smaller, local transactions managed by individual services. Each local transaction triggers the next step or compensating action if needed.

- **Types of Sagas**:
  - **Choreography**: Services trigger each other’s local transactions through events.
  - **Orchestration**: A centralized controller (orchestrator) coordinates the steps in the saga.

- **Pros**:
  - **Avoids Distributed Transactions**: Sagas allow for distributed transactions without needing distributed locks or complex transactions, improving scalability and resilience.
  - **Error Handling with Compensations**: If a transaction step fails, a compensating transaction can be executed to revert previous steps, making error recovery easier.
  - **Improved Scalability**: Sagas are generally lightweight and avoid locking resources across services, allowing for better performance.

- **Cons**:
  - **Increased Complexity**: Implementing sagas requires careful handling of compensating actions, particularly in complex workflows where data consistency is crucial.
  - **Eventual Consistency**: Sagas achieve eventual consistency, not immediate consistency, which may not suit applications requiring strict data consistency.
  - **Coordination Overhead**: The need for orchestration or choreography can lead to more complex coordination, debugging, and monitoring efforts.

---

### 4. **CQRS (Command Query Responsibility Segregation)**

CQRS separates read and write operations for data. In this pattern, commands (writes) and queries (reads) are handled by different models and data stores, allowing optimized data handling for each type of operation.

- **Pros**:
  - **Optimized Performance**: Read and write operations can be scaled and optimized independently. Queries, for instance, can use a read-optimized database, while writes use a different model.
  - **Supports Complex Business Logic**: By separating reads and writes, CQRS can help support complex, domain-specific logic for each side without one affecting the other.
  - **Enhanced Security**: CQRS allows more control over each type of access, enabling different access rules for read and write operations.

- **Cons**:
  - **Complex Data Synchronization**: Keeping data synchronized between the read and write models can be complex, often requiring event-based syncing and consistency management.
  - **Higher Operational Complexity**: The pattern increases infrastructure complexity, as it requires separate models, services, or databases for commands and queries.
  - **Eventual Consistency**: As with sagas, CQRS often achieves eventual consistency, which may not work for systems requiring real-time, strict data consistency.

---

**database per service** is generally the preferred approach in a microservices architecture. This preference is due to the following key advantages that better align with the principles and goals of microservices:

### 1. **Service Independence and Loose Coupling**
   - Each microservice can evolve independently, as it controls its own database schema. This independence avoids the tight coupling seen in a shared database setup, where schema changes can potentially disrupt multiple services.
   - When each service owns its data, it allows for **autonomous development** and deployment. This means services can change or deploy independently without worrying about breaking dependencies in a shared database.

### 2. **Scalability**
   - With a database per service, each microservice can be scaled individually. Some services may have high read demands (e.g., a product catalog in e-commerce), while others may be write-intensive (e.g., order processing).
   - Separate databases allow services to be optimized, replicated, or partitioned based on their individual requirements, enhancing scalability and performance.

### 3. **Fault Isolation**
   - A database issue or heavy query load in one service will not impact others. Each service can handle its data operations independently, reducing the risk of one service affecting the availability or performance of another.

### 4. **Better Security and Data Ownership**
   - Each service having its own database enables **better control over access and data security**. This approach allows for more fine-grained access policies specific to the service’s needs, reducing the risk of unauthorized access.
   - Ownership of data by each service also improves **data privacy** and security compliance, as each service can follow specific data-handling rules.

### 5. **Alignment with Microservices Philosophy**
   - A core goal of microservices is to divide functionality into independently deployable, modular units. A shared database goes against this by creating interdependencies at the data layer, undermining the modularity and autonomy microservices aim to achieve.
   - **Database per service** aligns well with **Domain-Driven Design (DDD)**, where services map closely to specific business domains or subdomains.

---

### Challenges of Database per Service

Despite the advantages, a **database per service** architecture introduces complexity, especially around **data consistency** and **distributed transactions**:

- **Eventual Consistency**: Since each service has its own database, ensuring data consistency across services can be challenging. This often requires adopting patterns like **Sagas** for distributed transactions, which manage workflows across services to achieve eventual consistency.

- **Data Synchronization and Replication**: Services sometimes need access to related data managed by other services. This can be managed through inter-service APIs or data replication mechanisms, but both approaches add complexity and potential for increased latency.

- **Operational Overhead**: Managing multiple databases adds to the complexity of infrastructure, monitoring, backup, and maintenance.

---

### When a Shared Database May Be Useful

There are cases where a **shared database** might be practical, particularly for **simple applications** with tightly related components, or in the early stages of development, where overhead from managing multiple databases outweighs the need for full independence. However, as the application grows, moving to a **database per service** setup is typically recommended.

---

### In Summary

While both approaches have their place, **database per service** is preferred in most microservices setups due to its alignment with microservices principles, offering better service autonomy, scalability, and fault isolation. The challenges of consistency and operational overhead can be mitigated through best practices and patterns, making it the recommended choice for scalable and resilient microservices architectures.

- **Saga** enables distributed transaction handling with error recovery, though it introduces complexity and potential latency.
- **CQRS** optimizes performance and flexibility for read/write operations but adds complexity in data management and synchronization.

Each pattern addresses specific needs and trade-offs, and their usage often depends on the application’s scalability, data consistency, and operational requirements.