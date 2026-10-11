# Availability in System Design

## 1. What Is Availability?

**Availability means a system is up and users can use it when they need it.**

For example, when you open a shopping app, its website and main features should be available instead of showing an error or staying offline.

## 2. Why Do We Need It?

If an application is unavailable, users cannot complete their work. For a business, this can mean lost orders, unhappy customers, or lost trust.

Availability is especially important for services such as payments, banking, shopping, and communication apps.

## 3. Uptime and Downtime

- **Uptime:** The time a system is working and available.
- **Downtime:** The time a system cannot provide its service.

Availability is often shown as a percentage. Higher availability means less downtime over time.

For example, **99.9% availability** means the system is available for 99.9% of the measured time. It does not mean that failures can never happen.

## 4. How Can We Improve Availability?

### Avoid one point of failure

If one server is the only place handling requests, its failure can make the whole application unavailable. Having another ready server can reduce this risk.

### Detect problems quickly

Health checks and monitoring help detect servers or services that are not working properly.

### Switch to a healthy component

A load balancer can stop sending new requests to a failed server and send them to a healthy one, when the system is configured to do so.

### Plan for failures and updates

Use safe deployments, backups where needed, and recovery plans. Remember that a backup helps restore data, but does not automatically keep an application online.

See [Reliability](./reliability.md) for more about handling failures and recovering safely.

## 5. Availability vs Reliability

| Availability | Reliability |
|---|---|
| Can users access the system? | Does the system work correctly and consistently? |
| Focuses on being up and usable. | Focuses on correct behaviour over time. |

A website can be available but unreliable. For example, it may open successfully but show the wrong order status.

## 6. High Availability

**High availability** means designing a system to reduce downtime and keep the service accessible when parts fail.

It often uses extra servers, health checks, and failover. These choices can improve availability, but they also add cost and complexity. The right level depends on how much downtime the application can accept.

## 7. Quick Revision

- **Availability:** The system is up and can be used when needed.
- **Uptime:** Time the service is working.
- **Downtime:** Time the service is unavailable.
- **High availability:** Design choices that reduce downtime.
- Redundant servers and health checks can help, but they do not guarantee zero downtime.
- Availability and reliability are related, but they are not the same.


### Still not understanding Watch this: [ Scalability vs Availability vs Reliability Explained | System Design Foundation](https://youtube.com/shorts/5iKZo1HzJFw?si=XCjMv4a0R-_uKEeu)

## Related Notes

- [Reliability](./reliability.md)
- [Scalability](./scalability.md)
- [Load Balancer](../02-components/load-balancer.md)
- [System Design Basics](../01-fundamentals/system-design-basics.md)
