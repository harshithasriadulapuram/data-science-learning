
# System Design Fundamentals

## 1. What Is System Design?

System design is the process of defining a software system's architecture, components, data flow, interfaces, and infrastructure to satisfy functional and non-functional requirements.

Examples:
- Designing a URL shortener.
- Designing a chat application.
- Designing an online shopping platform.
- Designing a notification service.
- Designing a file storage service.

## 2. Functional and Non-Functional Requirements

### Functional Requirements

Describe what the system must do.

Example for a URL shortener:
- Accept a long URL.
- Generate a short URL.
- Redirect users to the original URL.
- Optionally track clicks.

### Non-Functional Requirements

Describe how well the system should operate.

Examples:
- Low latency.
- High availability.
- Scalability.
- Reliability.
- Security.
- Maintainability.
- Data durability.

Always clarify both categories before designing a system.

## 3. Monolithic vs. Microservices Architecture

### Monolithic Architecture

The application's functionality is deployed as one main application.

Advantages:
- Simple to start with.
- Easier local development.
- Straightforward deployment for small systems.

Disadvantages:
- Large codebases can become difficult to maintain.
- Independent scaling of individual features is harder.
- A shared deployment can increase release risk.

### Microservices Architecture

The application is divided into independently deployable services.

Advantages:
- Services can be scaled independently.
- Teams can own separate services.
- Failures and deployments can sometimes be isolated.

Disadvantages:
- Network communication introduces complexity.
- Monitoring and deployment become harder.
- Distributed data consistency requires careful design.

**Interview tip:** Microservices are not automatically better. A well-structured monolith is often the right starting point.

## 4. Core System Components

### Client

The browser, mobile application, or another service that sends requests.

### DNS

Translates domain names into network addresses.

### Load Balancer

Distributes incoming traffic across application instances.

### Application Server

Executes business logic and handles requests.

### Database

Stores persistent application data.

### Cache

Stores frequently accessed data for faster retrieval.

### Message Queue

Allows components to communicate asynchronously.

### Object Storage

Stores files such as images, documents, and backups.

## 5. A Typical Web Application Architecture

```text
Client
  |
  v
DNS
  |
  v
Load Balancer
  |
  v
Application Servers
  |
  +---------> Cache
  |
  +---------> Database
  |
  +---------> Message Queue
                  |
                  v
             Background Worker
```

This is a conceptual architecture. Real systems may include API gateways, authentication services, CDNs, search systems, and other components.

## 6. Scalability

Scalability is the ability of a system to handle increasing workloads.

### Vertical Scaling

Increase the resources of one machine.

Examples:
- More RAM.
- More CPU cores.
- Faster storage.

Advantages:
- Relatively simple.
- Often requires fewer architectural changes.

Limitations:
- Hardware capacity has practical limits.
- A single machine may remain a failure point.

### Horizontal Scaling

Add more machines or application instances.

Advantages:
- Can increase capacity substantially.
- Supports redundancy when designed correctly.

Limitations:
- Requires load balancing.
- Shared state and coordination become more complicated.
- Database scaling may need separate strategies.

## 7. Latency and Throughput

### Latency

The time required to complete an operation.

Example: An API request takes 120 milliseconds.

### Throughput

The amount of work completed per unit of time.

Example: A service processes 2,000 requests per second.

A system can have high throughput but still exhibit high latency for individual requests.

## 8. Caching

Caching stores reusable results so they can be retrieved more quickly.

Common locations:
- Browser cache.
- CDN cache.
- Application cache.
- Distributed cache such as Redis.
- Database buffer cache.

Common strategies:
- Cache-aside.
- Read-through.
- Write-through.
- Write-behind.

Important considerations:
- Expiration time, or TTL.
- Cache invalidation.
- Stale data.
- Cache stampedes.
- Memory limits.

Caching improves performance in many systems, but introduces consistency and invalidation challenges.

## 9. Database Fundamentals

### SQL Databases

Examples: PostgreSQL and MySQL.

Useful when:
- Data has structured relationships.
- Transactions are important.
- Complex joins and queries are required.

### NoSQL Databases

Examples: document, key-value, column-family, and graph databases.

Useful when their data models and scaling characteristics fit the workload.

### Replication

Copies data across database instances to improve availability or read capacity.

### Sharding

Partitions data across multiple database nodes.

Replication and sharding solve different problems and may be used together.

## 10. CAP Theorem

The CAP theorem concerns trade-offs in distributed data systems during a network partition.

- **Consistency:** Reads reflect a single, up-to-date view according to the system's consistency guarantees.
- **Availability:** Every request to a non-failing node receives a response.
- **Partition tolerance:** The system continues operating despite certain network communication failures.

During a network partition, a distributed system must trade off between consistency and availability as defined by CAP.

Do not interpret CAP as saying that a system can only ever have two of the three properties in all circumstances.

## 11. Synchronous vs. Asynchronous Processing

### Synchronous Processing

The caller waits for the operation to complete.

Useful for:
- Fetching a user profile.
- Validating login credentials.
- Returning search results.

### Asynchronous Processing

The caller can continue while work happens separately.

Useful for:
- Sending emails.
- Generating reports.
- Processing uploaded files.
- Running lengthy data pipelines.

A message queue can decouple request handling from background work.

## 12. Reliability and Fault Tolerance

Reliable systems anticipate failures.

Useful techniques include:
- Timeouts.
- Bounded retries with backoff.
- Circuit breakers.
- Health checks.
- Redundancy.
- Graceful degradation.
- Monitoring and alerting.
- Backups and recovery testing.

Retries must be designed carefully because repeating a non-idempotent operation can cause duplicate side effects.

## 13. Security Fundamentals

- Authenticate clients.
- Authorize each protected operation.
- Use HTTPS.
- Validate untrusted input.
- Apply rate limits.
- Store secrets securely.
- Encrypt sensitive data where appropriate.
- Follow least-privilege access.
- Log security-relevant events safely.
- Avoid exposing internal errors.

Security should be included in the initial design rather than added only at the end.

## 14. A System Design Interview Framework

Follow this sequence:

1. Clarify the problem and scope.
2. Identify functional requirements.
3. Identify non-functional requirements.
4. Estimate traffic and storage if relevant.
5. Define APIs and data models.
6. Draw a high-level architecture.
7. Explain key component choices.
8. Identify bottlenecks and failure modes.
9. Discuss scaling and consistency trade-offs.
10. Address security, monitoring, and recovery.

Explain why you choose each component instead of simply listing technologies.

## 15. Practice Problems

Start with these systems:

- [ ] URL shortener.
- [ ] Pastebin-like application.
- [ ] File storage service.
- [ ] Notification service.
- [ ] Chat application.
- [ ] News feed.
- [ ] Rate limiter.
- [ ] E-commerce product catalog.
- [ ] Video streaming platform.
- [ ] Distributed job scheduler.

For each problem, write requirements, draw an architecture, define APIs, choose a data model, and explain the main trade-offs.

## 16. Interview Questions

1. What is system design?
2. What is the difference between functional and non-functional requirements?
3. Compare monolithic and microservices architectures.
4. Explain vertical and horizontal scaling.
5. What is a load balancer?
6. Why is caching useful?
7. Compare replication and sharding.
8. What is the difference between latency and throughput?
9. When would you use a message queue?
10. Explain the CAP theorem.
11. What is fault tolerance?
12. Why are timeouts important?
13. How can you prevent duplicate processing?
14. How would you identify a system bottleneck?
15. How would you design a highly available API?

## Key Takeaway

Good system design is not about using the most technologies. It is about meeting requirements, understanding bottlenecks, managing failure, and making clear trade-offs between cost, performance, reliability, and complexity.
