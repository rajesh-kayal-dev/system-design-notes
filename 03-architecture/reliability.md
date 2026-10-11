# Reliability in System Design

## 1. What Is Reliability?

**Reliability means a system works correctly and consistently over time.** Users should be able to trust it to do what it is supposed to do.

For example, when a customer pays for an order, the system should record the payment correctly and should not create the same payment twice.

## 2. Why Do We Need It?

A system may be fast but still be unreliable if it often crashes, loses data, or gives incorrect results.

Good reliability helps to:
- Reduce failures and incorrect results.
- Protect important data.
- Keep important features working when problems happen.
- Build user trust.

## 3. Reliability, Availability, and Fault Tolerance

These terms are related, but they are not the same:

| Term | Simple meaning |
|---|---|
| **Reliability** | The system works correctly and consistently. |
| **Availability** | The system is up and can be used when needed. |
| **Fault tolerance** | The system can keep providing service, or limit the impact, when part of it fails. |

**Example:** A website may be available, but if it shows the wrong account balance, it is not working reliably.

## 4. How Can We Make a System More Reliable?

### Use backup components

Avoid depending on just one server or component for important work. A second healthy server may take over if the first one fails.

### Detect failures

Health checks and monitoring help the system or team notice when a server is not responding correctly.

### Use timeouts and careful retries

A timeout stops a request from waiting forever. A retry may help when a temporary problem occurs, but retries should be limited. For actions such as payments, the system must also prevent a retry from causing the same action twice.

### Back up important data

Backups help recover data if it is deleted, damaged, or lost. Backups should be tested so the team knows they can be restored.

### Handle errors safely

If one part fails, return a clear error or provide a safe alternative instead of leaving the whole application in a broken state.

## 5. Simple Example: One Server Fails

Imagine an application runs on two servers behind a load balancer. If one server stops responding, health checks can identify the problem and the load balancer can send new requests to the healthy server.

```text
                  ┌── Server A (failed)
User → Load Balancer
                  └── Server B (healthy) → handles new requests
```

This can reduce downtime, but it does not guarantee that every request will succeed. The system still needs proper error handling and data protection.

See [Load Balancer](../02-components/load-balancer.md) for more detail.

## 6. Reliability Is a Design Choice

More backups and extra servers can improve reliability, but they also add cost and complexity. Choose protection based on how important the system is and what a failure would mean.

For example, a payment system needs stronger protection against incorrect or duplicate transactions than a simple personal notes app.

## 7. Quick Revision

- **Reliability:** Works correctly and consistently over time.
- **Availability:** Can be accessed when needed.
- **Fault tolerance:** Limits the impact of component failures.
- Health checks help detect problems; backups help recover data.
- Timeouts and retries can help, but retries must not repeat important actions by mistake.
- Reliable systems need a balance between protection, cost, and complexity.


### Still not understanding Watch this: [ Scalability vs Availability vs Reliability Explained | System Design Foundation](https://youtube.com/shorts/5iKZo1HzJFw?si=XCjMv4a0R-_uKEeu)

## Related Notes

- [System Design Basics](../01-fundamentals/system-design-basics.md)
- [Load Balancer](../02-components/load-balancer.md)
- [Message Queues](../02-components/message-queues.md)
- [Monolith vs Microservices](./monolith-vs-microservices.md)
- [Scalability](./scalability.md)
