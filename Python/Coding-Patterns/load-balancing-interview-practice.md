
# Load Balancing — Interview Practice

## 1. What Is Load Balancing?

Load balancing distributes incoming requests across multiple servers or application instances.

Instead of sending every request to one server, a load balancer routes requests to available backend instances.

Benefits:
- Distributes workload.
- Improves capacity.
- Reduces overload on individual servers.
- Supports high availability when combined with redundancy.
- Allows backend instances to be added or removed.

## 2. Basic Architecture

```text
                 Clients
                    |
                    v
              Load Balancer
               /     |     \
              v      v      v
           Server A Server B Server C
              |      |      |
              +------+------+
                     |
                     v
                  Database
```

The load balancer selects a backend according to its routing algorithm and health information.

A load balancer alone does not guarantee high availability. It must also avoid single points of failure and rely on appropriately designed backend services.

## 3. Types of Load Balancers

### Layer 4 Load Balancing

Operates at the transport layer, typically using TCP or UDP connection information.

It can route traffic based on network addresses and ports.

### Layer 7 Load Balancing

Operates at the application layer, commonly handling HTTP and HTTPS.

It can route traffic using:
- URL paths.
- Hostnames.
- HTTP headers.
- Other application-level information.

Example:
- `/api/users` routes to a user service.
- `/api/products` routes to a product service.

Layer 7 routing provides more application context but may require more processing.

## 4. Common Load-Balancing Algorithms

### A. Round Robin

Requests are assigned to servers in sequence.

Example:

```text
Request 1 -> Server A
Request 2 -> Server B
Request 3 -> Server C
Request 4 -> Server A
```

Useful when backend servers have similar capacity and requests have reasonably similar costs.

### B. Weighted Round Robin

Servers receive traffic according to configured weights.

Example:

```text
Server A: weight 5
Server B: weight 3
Server C: weight 2
```

Over time, traffic is distributed approximately in proportion to these weights.

Useful when servers have different capacities.

### C. Least Connections

Routes new connections toward the server with the fewest active connections, according to the load balancer's implementation.

Useful when connections remain open for different lengths of time.

### D. Weighted Least Connections

Considers both active connections and server weights.

This can account for differences in backend capacity.

### E. IP Hash

Uses a hash of the client's IP address to select a backend.

It may help route a client consistently to the same server, but mapping can change when the backend pool changes.

### F. Least Response Time

Uses response-time measurements, sometimes combined with active connection counts, to choose a backend.

The exact algorithm depends on the product.

## 5. Health Checks

A load balancer can periodically check whether a backend is healthy.

Common checks include:
- TCP connection checks.
- HTTP endpoint checks.
- Application readiness checks.

For example:

```http
GET /health
```

A healthy backend might return:

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

An unhealthy backend can be temporarily removed from routing until it passes the required recovery checks.

**Important:** Liveness and readiness are different concepts. A process can be alive but not ready to serve traffic.

## 6. Session Persistence

Some applications maintain session state on individual servers.

Session persistence, sometimes called sticky sessions, attempts to send a client to the same backend across requests.

Advantages:
- Can support applications that rely on local session state.

Disadvantages:
- May cause uneven traffic distribution.
- Makes failover more difficult.
- Can complicate scaling.

A common alternative is storing session state in a shared session store so requests can be handled by different backend instances.

## 7. Reverse Proxy vs. Load Balancer

A reverse proxy accepts client requests and forwards them to backend services.

A load balancer distributes traffic across multiple backends.

These roles overlap: a Layer 7 reverse proxy can also perform load balancing.

Reverse proxies may additionally handle TLS termination, routing, compression, and request filtering.

## 8. Simple Python Load Balancer Simulation

This example simulates round-robin selection. It does not implement a real network proxy.

```python
class RoundRobinLoadBalancer:
    def __init__(self, servers):
        if not servers:
            raise ValueError("At least one server is required")

        self.servers = list(servers)
        self.index = 0

    def next_server(self):
        server = self.servers[self.index]
        self.index = (self.index + 1) % len(self.servers)
        return server


lb = RoundRobinLoadBalancer([
    "server-A",
    "server-B",
    "server-C"
])

for request_id in range(1, 8):
    server = lb.next_server()
    print(f"Request {request_id} -> {server}")
```

Expected output:

```text
Request 1 -> server-A
Request 2 -> server-B
Request 3 -> server-C
Request 4 -> server-A
Request 5 -> server-B
Request 6 -> server-C
Request 7 -> server-A
```

The simulation assumes a static list of healthy servers. A real load balancer also needs concurrency handling, health checks, connection management, failure handling, and network communication.

## 9. High Availability

High availability aims to keep a service accessible despite component failures.

Common techniques:
- Multiple backend instances.
- Redundant load balancers.
- Health checks and automatic failover.
- Deployment across availability zones.
- Graceful degradation.
- Monitoring and alerting.
- Tested disaster-recovery procedures.

Avoid placing all critical components in one failure domain.

## 10. Common Problems

### Uneven Load

Some servers receive more work than others.

Possible responses:
- Use a suitable routing algorithm.
- Configure server weights.
- Monitor CPU, memory, latency, and request volume.

### Slow Backend

A server may be reachable but respond slowly.

Possible responses:
- Configure appropriate timeouts.
- Track backend latency.
- Remove unhealthy instances according to health policies.
- Investigate the underlying bottleneck.

### Single Point of Failure

If the only load balancer fails, the service may become unavailable.

Possible response:
- Use redundant load-balancing infrastructure with an appropriate failover mechanism.

### Long-Lived Connections

WebSockets and long-lived connections may not distribute work evenly using simple request counts.

Possible responses:
- Choose algorithms suitable for the workload.
- Monitor connection counts and resource usage.
- Plan graceful connection draining during deployments.

## 11. Interview Questions

1. What is load balancing?
2. Why do distributed systems use load balancers?
3. Compare Layer 4 and Layer 7 load balancing.
4. Explain round robin and weighted round robin.
5. When is least-connections routing useful?
6. What is IP hashing?
7. Why are health checks necessary?
8. What is the difference between liveness and readiness?
9. What are sticky sessions?
10. How can a load balancer become a single point of failure?
11. What is the difference between a reverse proxy and a load balancer?
12. How would you handle slow or unhealthy backend servers?

## 12. Practice Tasks

- [ ] Implement round-robin server selection.
- [ ] Add weighted round-robin selection.
- [ ] Simulate unhealthy servers and skip them.
- [ ] Write tests for empty server lists and routing order.
- [ ] Draw an architecture with redundant load balancers.
- [ ] Explain which routing algorithm you would choose for long-lived connections.

## Key Takeaway

Load balancing improves traffic distribution and can support scalability and availability. The right design depends on backend capacity, request patterns, connection behavior, health checks, and failure-handling requirements.
