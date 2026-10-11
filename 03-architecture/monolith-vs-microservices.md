# Monolith vs Microservices

## 1. What Is a Monolith?

A **monolith** is an application where the main features are built and deployed as one unit.

For example, a food delivery app may have user, order, payment, and delivery features in one backend application.

If we update one feature, we usually deploy the application again as a whole.

## 2. What Are Microservices?

**Microservices** split an application into smaller services. Each service handles a specific business task and can be developed and deployed separately.

For example, a food delivery app may have separate User, Order, Payment, and Delivery services. They communicate through APIs or messages.

## 3. See the Difference

```text
MONOLITH                         MICROSERVICES

      One application             User Service
   ┌──────────────────┐               │
   │ Users            │          Order Service
   │ Orders           │               │
   │ Payments         │         Payment Service
   │ Delivery         │               │
   └──────────────────┘        Delivery Service

 Features are deployed       Services can be deployed
 together as one unit.       separately.
```

## 4. Main Differences

| Monolith | Microservices |
|---|---|
| One main application | Multiple smaller services |
| Usually simpler to build and run at first | Needs more setup and coordination |
| Often deployed as one unit | Services can be deployed separately |
| Scaling usually affects the whole application | Individual services can be scaled when needed |
| A problem in one area can affect the whole app, depending on its design | Service failures can be isolated, but only with careful design |
| Easier to debug in smaller applications | Debugging requests across services can be harder |

## 5. Advantages and Challenges

### Monolith

**Advantages**
- Simple to develop, test, and deploy at the beginning.
- Communication between features is straightforward.
- Usually needs less infrastructure and operational work.

**Challenges**
- As the application grows, the codebase can become harder to manage.
- A small change may require deploying the whole application.
- Scaling one busy feature may mean scaling the entire application.

### Microservices

**Advantages**
- Teams can work on separate services.
- A busy service can be scaled without scaling every service.
- Services can be deployed independently.

**Challenges**
- Services need reliable communication with each other.
- Monitoring, testing, deployment, and debugging become more complex.
- Data consistency and failures between services need careful handling.
- Infrastructure and maintenance costs may increase.

## 6. Which One Should You Choose?

Choose a **monolith** when:
- You are building a small application or an early version of a product.
- One team can manage the application comfortably.
- You want to build and release features quickly with less operational work.

Consider **microservices** when:
- The product has grown enough that separate parts have different scaling needs.
- Multiple teams need to own and release services independently.
- The organization can handle the added deployment and monitoring complexity.

Microservices are not automatically better. Start with the simplest design that meets the requirements, and split services when there is a clear reason.

## 7. Quick Revision

- **Monolith:** One application containing the main features, usually deployed as one unit.
- **Microservices:** Multiple smaller services, each responsible for a specific business task.
- **Main trade-off:** Simplicity with a monolith versus independent scaling and deployment with more complexity in microservices.
- **Remember:** Choose based on the application's needs, not because one architecture is more popular.

## Related Notes

- [Scalability](./scalability.md)
- [Reliability](./reliability.md)
- [Message Queues](../02-components/message-queues.md)
- [System Design Basics](../01-fundamentals/system-design-basics.md)
