# Fault Tolerance in System Design

## 1. What Is Fault Tolerance?

**Fault tolerance means a system can keep providing its service, or limit the impact, when one of its parts fails.**

For example, if one backend server stops working, another healthy server can handle new requests so the whole application does not go down.

## 2. Why Do We Need It?

Servers, databases, networks, and other components can fail. Fault tolerance helps to:

- Reduce the impact of failures.
- Keep important features working.
- Avoid depending on one component only.
- Help users continue their work during a problem.

## 3. How Does It Work?

A fault-tolerant design prepares for failures instead of assuming everything will always work.

### Use backup components

Run another server or component that can take over when the main one fails. This is called **redundancy**.

### Detect the failure

Health checks and monitoring help identify when a component is not working properly.

### Switch to a healthy component

When possible, the system sends new requests to a working server or service. This is called **failover**.

```text
                 ┌── Server A (failed)
Users → Load Balancer
                 └── Server B (healthy) → handles new requests
```

This can reduce disruption, but it does not guarantee that every request will succeed or that no data will be lost.

## 4. Other Ways to Handle Failures

- **Timeouts:** Stop waiting for a response forever.
- **Limited retries:** Try again after a temporary failure, but avoid endless retries or repeating the same action twice.
- **Graceful degradation:** Keep the main features working when an optional feature is down. For example, a shop may still accept orders even if product recommendations are unavailable.
- **Backups and recovery:** Restore lost or damaged data. Backups help recovery, but do not automatically keep the service online.

Use these techniques where they match the system's needs. Not every application needs every technique.

## 5. Fault Tolerance vs Reliability vs Availability

| Term | Simple meaning |
|---|---|
| **Fault tolerance** | Limits the impact when a component fails. |
| **Reliability** | The system works correctly and consistently over time. |
| **Availability** | Users can access and use the system when needed. |

These ideas are connected. Fault tolerance can improve availability and reliability, but it does not guarantee either one.

## 6. When Is It Important?

Fault tolerance matters most when an outage or failure has a serious impact, such as in payment systems, banking, shopping, and communication services.

Extra protection adds cost and complexity. Choose the level of protection based on how much failure the application can safely handle.

## 7. Quick Revision

- **Fault tolerance:** Keep the service working, or reduce the impact, when part of the system fails.
- **Redundancy:** Have extra components available.
- **Failover:** Switch work to a healthy component after a failure.
- Timeouts, limited retries, graceful degradation, and recovery plans help handle problems.
- Fault tolerance can reduce disruption, but it cannot promise zero failures.

### Still not understanding Watch this: [ Fault tolerance kya hota hai? Simple explanation](https://youtu.be/tNEp8Y7XlJc)





## Related Notes

- [Reliability](./reliability.md)
- [Availability](./availability.md)
- [Scalability](./scalability.md)
- [Load Balancer](../02-components/load-balancer.md)
- [Message Queues](../02-components/message-queues.md)
