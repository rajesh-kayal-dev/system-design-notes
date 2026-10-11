# Design Pattern Concept Stories

These stories explain each pattern through familiar app situations. Company and app names are used only to make the examples relatable; they are not claims about those companies' actual internal architecture.

## What is the Timeout Pattern?

> **Detailed notes:** [Timeout Pattern](./timeout-pattern.md)

Imagine you place an order on Amazon and click **Pay**, but the screen keeps loading. The app should not wait forever. A timeout sets a limit on how long the app waits for a response. When that time ends, the app can tell you that the payment result is not confirmed yet and help you check its status.

**Remember:** a timeout does not always mean the payment failed. Payment successful ho sakti hai, but the response may have arrived too late. That is why the system should check the payment status before asking you to pay again.

**Related concepts:** [Retry Pattern](./retry-pattern.md) · [Circuit Breaker](./circuit-breaker.md) · [Idempotency](./idempotency.md)

## What is the Retry Pattern?

> **Detailed notes:** [Retry Pattern](./retry-pattern.md)

Imagine Netflix is loading information about a movie, but the request fails because of a temporary network problem. The system can try the request again instead of giving up immediately. This is called a retry.

Retry useful hai jab problem temporary ho, but it should not continue forever. The system should set a limit and wait between attempts. Otherwise, too many retries can put extra pressure on a service that is already struggling. Retry is not the right solution for every type of error.

**Related concepts:** [Timeout Pattern](./timeout-pattern.md) · [Circuit Breaker](./circuit-breaker.md) · [Idempotency](./idempotency.md)

## What is the Circuit Breaker Pattern?

> **Detailed notes:** [Circuit Breaker](./circuit-breaker.md)

Imagine a shopping app uses a service to recommend products, but that service keeps failing. If the app continues calling it on every request, the app may become slower and the extra requests may make the problem worse. The Circuit Breaker Pattern temporarily stops calls to a service after repeated failures. After a short wait, it allows a test request to check whether the service has recovered.

Agar service abhi bhi fail ho rahi hai, circuit calls ko phir se block kar sakta hai. If the test succeeds, normal calls can resume. The pattern does not repair the service; it helps protect the rest of the application.

**Related concepts:** [Timeout Pattern](./timeout-pattern.md) · [Retry Pattern](./retry-pattern.md) · [Microservices](../03-architecture/monolith-vs-microservices.md)

## What is Idempotency?

> **Detailed notes:** [Idempotency](./idempotency.md)

Imagine you click **Place Order** on Amazon, but the page seems stuck, so you click it again. The system should avoid creating two orders for the same operation. One way to help prevent this is an **idempotency key**, a unique value that identifies the operation. If the same request arrives again with the same key, the server can return the saved result instead of processing the order again.

Simple words mein, same request repeat hone par uska effect dobara nahi hona chahiye. This is especially important for payments, orders, bookings, and refunds.

**Related concepts:** [Retry Pattern](./retry-pattern.md) · [Timeout Pattern](./timeout-pattern.md) · [Saga Pattern](./saga-pattern.md)

## What is Event-Driven Architecture?

> **Detailed notes:** [Event-Driven Architecture](./event-driven-architecture.md)

Imagine you place an order on Amazon. After an order is placed, a system may need to update inventory, send an email or SMS, and record the order for analytics. Without Event-Driven Architecture, the Order Service might have to call each of those services directly. That can make the process slower and create more direct dependencies between services.

Instead, the Order Service can publish an **`OrderPlaced` event** to a message broker such as Kafka or RabbitMQ. Services interested in this event receive it and do their own tasks. The inventory service updates stock, the notification service sends a message, and the analytics service records the event.

Yahi main idea hai: the Order Service announces that the order was placed, and other interested services react to that event. Services that do not need the event can ignore it. Events can be delayed or delivered more than once, so the system must handle failures and duplicates carefully.

**Related concepts:** [Message Queues](../02-components/message-queues.md) · [Synchronous vs. Asynchronous](../03-architecture/sync-vs-async.md) · [Idempotency](./idempotency.md)

## What is the API Gateway Pattern?

> **Detailed notes:** [API Gateway](./api-gateway.md)

Imagine a shopping app has separate backend services for users, products, and orders. Without a common entry point, the app may need to contact each service separately. With an API Gateway, the app sends requests to one entry point, and the gateway forwards each request to the correct service. Depending on its setup, the gateway may also check authentication or apply rate limits.

Think of it as a reception desk that directs each request to the right department. Client ko har backend service ka address separately manage nahi karna padta. Business rules, such as whether an order can be cancelled, should still stay in the relevant backend service.

**Related concepts:** [API Design](../01-fundamentals/api-design.md) · [Load Balancer](../02-components/load-balancer.md) · [Rate Limiting](../02-components/rate-limiting.md)

## What is the Cache-Aside Pattern?

> **Detailed notes:** [Cache-Aside Pattern](./cache-aside-pattern.md)

Imagine you open a popular product page on Amazon. The application checks the cache first. If the product details are not there, it reads them from the database, saves a copy in the cache, and returns the details. When someone requests the same product again, the app may get the details from the cache faster.

This is the Cache-Aside Pattern: check the cache first, and fetch from the database when the data is missing. Agar product ka price change ho jaye, cached copy old ho sakti hai, so the application needs a way to update or remove stale data.

**Related concepts:** [Caching](../02-components/caching.md) · [Redis](../02-components/redis.md) · [Databases](../01-fundamentals/databases.md)

## What is the Saga Pattern?

> **Detailed notes:** [Saga Pattern](./saga-pattern.md)

Imagine you order food in a delivery app. The order is created, the payment succeeds, but then the restaurant rejects the order. The system needs to handle what happened earlier, for example by cancelling the order and refunding the payment. The Saga Pattern breaks a business process across multiple services into smaller steps and defines what to do if a later step fails.

The refund is called a **compensating action** because it counteracts an earlier action. Dhyan rahe, this is not the same as undoing a single database transaction. The original payment happened, and the system performs a new refund action to return the money. Saga is useful when one business process involves multiple services that manage their own data.

**Related concepts:** [Event-Driven Architecture](./event-driven-architecture.md) · [Idempotency](./idempotency.md) · [Message Queues](../02-components/message-queues.md) · [Microservices](../03-architecture/monolith-vs-microservices.md)
