# Scalability in System Design

## 1. What Is Scalability?

**Scalability is a system's ability to handle more work when demand grows.** That work may mean more users, more requests, or more data.

For example, a shopping website may work well on a normal day but become slow during a big sale. A scalable design helps it handle the extra traffic.

## 2. Why Do We Need It?

As an application grows, it may need to handle:
- More users using it at the same time.
- More requests to the backend.
- More data to store and process.

If the system cannot handle the extra work, it may become slow or stop responding.

## 3. Two Ways to Scale

### Vertical Scaling (Scale Up)

**Vertical scaling means giving one server more resources**, such as CPU or memory.

Example: Upgrade a server from 8 GB RAM to 32 GB RAM.

**Benefits:** Simple to start with and may require few application changes.

**Limitations:** A server has limits, and a more powerful server can cost more.

### Horizontal Scaling (Scale Out)

**Horizontal scaling means adding more servers and sharing the work between them.**

Example: Run three backend servers instead of one and use a load balancer to distribute requests.

```text
                 ┌── Backend Server 1
Users → Load Balancer ── Backend Server 2
                 └── Backend Server 3
```

**Benefits:** Can handle more traffic and gives the system more options if one server fails.

**Limitations:** More servers need coordination, monitoring, and suitable application design.

See [Load Balancer](../02-components/load-balancer.md) for how requests are shared between servers.

## 4. Vertical vs Horizontal Scaling

| Vertical scaling | Horizontal scaling |
|---|---|
| Makes one server more powerful | Adds more servers |
| Usually simpler to set up | Needs the work shared between servers |
| Limited by one server's capacity | Can grow by adding servers, within the system's limits |
| Often a good first step for a small system | Useful when traffic grows or more capacity is needed |

## 5. What About the Database?

Adding backend servers does not automatically make the whole system scalable. If every server sends too many queries to one database, the database may become the bottleneck.

A scalable design checks where the real limit is and improves that part. Depending on the need, this might involve better queries, [caching](../02-components/caching.md), database improvements, or more advanced database scaling.

## 6. When Should You Scale?

Do not add servers just because an application might become popular. First, look for signs such as slow responses, high resource usage, or growing request traffic. Then improve the part that is limiting the system.

Start with the simplest design that meets the current needs. Add complexity when there is a clear reason.

## 7. Quick Revision

- **Scalability:** The ability to handle more work as demand grows.
- **Vertical scaling:** Give one server more resources.
- **Horizontal scaling:** Add more servers and share the work.
- A load balancer can distribute requests across backend servers.
- More backend servers do not solve every problem; the database or another component may become the bottleneck.
- Choose a scaling approach based on the system's actual needs and limits.

### Still not understanding Watch this: [ Scalability vs Availability vs Reliability Explained | System Design Foundation](https://youtube.com/shorts/5iKZo1HzJFw?si=XCjMv4a0R-_uKEeu)

## Related Notes

- [System Design Basics](../01-fundamentals/system-design-basics.md)
- [Load Balancer](../02-components/load-balancer.md)
- [Caching](../02-components/caching.md)
- [Reliability](./reliability.md)
- [Monolith vs Microservices](./monolith-vs-microservices.md)
