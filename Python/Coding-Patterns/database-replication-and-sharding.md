
# Database Replication and Sharding

## 1. Introduction

As an application grows, one database server may struggle to handle increasing traffic, storage, and availability requirements.

Two common database scaling techniques are:

- **Replication:** Maintain copies of data on multiple database servers.
- **Sharding:** Partition data across multiple database servers.

They solve different problems and can be used together.

## 2. What Is Database Replication?

Replication copies data from one database server to one or more other servers.

A common architecture has a primary database and multiple replicas.

```text
             Application
                  |
                  v
             Primary DB
              /       \
             v         v
         Replica 1  Replica 2
```

In a typical primary-replica setup:
- Writes go to the primary.
- Changes are propagated to replicas.
- Reads may be served by replicas.

The exact write and read behavior depends on the database architecture.

### Benefits

- Can increase read capacity.
- Provides additional copies of data.
- Can support failover.
- Helps distribute read traffic.

### Challenges

- Replication lag.
- Failover complexity.
- Additional infrastructure cost.
- Potentially stale reads.
- Operational and consistency considerations.

## 3. Synchronous vs. Asynchronous Replication

### Synchronous Replication

The primary waits for acknowledgment from one or more replicas before reporting a successful commit, according to the configured acknowledgment policy.

Advantages:
- Can provide stronger durability or consistency guarantees.

Disadvantages:
- Can increase write latency.
- Replica or network failures may affect write availability.

### Asynchronous Replication

The primary acknowledges a write before replicas necessarily receive it.

Advantages:
- Often provides lower write latency.
- The primary can continue while replicas catch up.

Disadvantages:
- Replicas may return stale data.
- Recent acknowledged writes may be lost in some failure scenarios.

The precise guarantees depend on the database and replication configuration.

## 4. What Is Database Sharding?

Sharding divides a dataset into smaller partitions distributed across multiple database servers.

Each shard stores only part of the overall dataset.

```text
                Application
                     |
                     v
                Shard Router
               /      |      \
              v       v       v
           Shard 1  Shard 2  Shard 3
           Users    Users    Users
           1-1000   1001-2000 2001-3000
```

The ranges above are illustrative. Production sharding strategies may use hash-based partitioning, ranges, or other schemes.

### Benefits

- Distributes storage across servers.
- Can increase write and read capacity.
- Reduces the data handled by each shard.
- Can support horizontal scaling.

### Challenges

- Choosing a good shard key.
- Rebalancing data as the system grows.
- Cross-shard joins and transactions.
- Uneven data distribution.
- More complicated backups and operations.

## 5. Common Sharding Strategies

### A. Range-Based Sharding

Assign data according to a range of values.

Example:

```text
user_id 1-1000       -> Shard A
user_id 1001-2000    -> Shard B
user_id 2001-3000    -> Shard C
```

Advantages:
- Straightforward range queries.
- Easy to understand.

Disadvantages:
- Some ranges may receive much more traffic.
- New records can overload a particular shard if keys are assigned sequentially.

### B. Hash-Based Sharding

Hash the shard key and use the result to select a shard.

Conceptual example:

```python
def choose_shard(user_id, number_of_shards):
    return hash(user_id) % number_of_shards
```

This illustrates the idea, but Python's built-in `hash()` is not suitable for a stable production routing scheme across processes or deployments.

Advantages:
- Can distribute records more evenly.

Disadvantages:
- Range queries can require contacting multiple shards.
- Changing the shard count can remap many records with simple modulo hashing.

### C. Directory-Based Sharding

Use a mapping service or directory to determine which shard owns a record.

Advantages:
- Flexible placement and rebalancing.

Disadvantages:
- The directory must be highly available and kept consistent.
- Routing adds complexity.

## 6. What Is a Shard Key?

A shard key determines how records are assigned to shards.

A good shard key should consider:
- Even data distribution.
- Write and read traffic.
- Common query patterns.
- Cardinality.
- Whether queries can be routed to a single shard.
- Growth and rebalancing requirements.

A poor shard key can create a **hot shard**, where one database receives a disproportionate share of traffic.

For example, sharding all activity by a very popular tenant may overload one shard even if other shards are lightly used.

## 7. Replication vs. Sharding

| Feature | Replication | Sharding |
|---|---|---|
| Main purpose | Copy data | Partition data |
| Data placement | Multiple copies | Different partitions |
| Read scaling | Often helps | Can help |
| Write scaling | Usually limited in primary-replica designs | Can improve with distributed writes |
| Storage | Stores duplicate data | Distributes dataset |
| Main challenge | Consistency and failover | Partitioning and cross-shard operations |

Replication and sharding are not mutually exclusive. Each shard can have its own replicas.

## 8. Read Replicas and Stale Reads

Suppose a user updates a profile:

1. The write succeeds on the primary.
2. Replication is still in progress.
3. A subsequent read reaches a replica.
4. The replica returns the older profile.

This is a stale read.

Possible strategies include:
- Reading critical data from the primary.
- Using session-consistency mechanisms when supported.
- Waiting for replication to reach a required position.
- Using stronger consistency guarantees where necessary.

The correct strategy depends on the application and database.

## 9. Simple Shard-Routing Simulation

This learning example uses deterministic modulo routing.

```python
def choose_shard(user_id, number_of_shards):
    if number_of_shards <= 0:
        raise ValueError("number_of_shards must be positive")

    if user_id < 0:
        raise ValueError("user_id must be non-negative")

    return user_id % number_of_shards


number_of_shards = 4

for user_id in range(10):
    shard_id = choose_shard(user_id, number_of_shards)
    print(f"User {user_id} -> Shard {shard_id}")
```

Example output:

```text
User 0 -> Shard 0
User 1 -> Shard 1
User 2 -> Shard 2
User 3 -> Shard 3
User 4 -> Shard 0
User 5 -> Shard 1
User 6 -> Shard 2
User 7 -> Shard 3
User 8 -> Shard 0
User 9 -> Shard 1
```

This demonstrates routing only. It does not implement a database, data migration, replication, failure recovery, or a production-grade sharding algorithm.

## 10. When Should You Use Each?

### Consider Replication When

- Read traffic is growing.
- Additional copies are needed for availability.
- Read replicas can tolerate the required consistency model.
- The primary can still handle the write workload.

### Consider Sharding When

- A single database cannot meet storage or throughput requirements.
- Writes need to be distributed across multiple database nodes.
- The workload can be partitioned effectively.
- The team can manage routing, rebalancing, and distributed operations.

Before introducing sharding, investigate query optimization, indexing, caching, vertical scaling, and simpler replication approaches.

## 11. Interview Questions

1. What is database replication?
2. What is database sharding?
3. How do replication and sharding differ?
4. Explain synchronous and asynchronous replication.
5. What is replication lag?
6. What is a shard key?
7. Compare range-based and hash-based sharding.
8. What is a hot shard?
9. How can sharding complicate joins and transactions?
10. How would you handle stale reads?
11. How can replication support failover?
12. Can replication and sharding be used together?
13. What problems can occur during shard rebalancing?
14. When is sharding unnecessary?

## 12. Practice Tasks

- [ ] Draw a primary-replica database architecture.
- [ ] Explain what happens when a replica lags behind.
- [ ] Implement the shard-routing simulation.
- [ ] Compare range-based and hash-based sharding.
- [ ] Design a shard key for a multi-tenant application.
- [ ] Explain how to detect and address a hot shard.
- [ ] Design an architecture where every shard has a replica.

## Key Takeaway

Replication creates additional copies of data, while sharding distributes different portions of data across servers. Choose the strategy based on the actual bottleneck, required consistency, workload, and operational complexity.
