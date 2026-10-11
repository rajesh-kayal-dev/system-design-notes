# Retry Pattern

## 1. What is the Retry Pattern?

The **Retry Pattern** means trying an operation again when it fails, instead of giving up after the first attempt.

For example, your backend sends a request to a payment service, but the service is temporarily unavailable. Your backend may wait briefly and try again.

**In simple words:** Retry means trying again after a failure.

A retry can help when a problem is temporary. It cannot guarantee success, and it should not be used for every failure.

## 2. Why do we need retries?

Services can fail temporarily because of network problems, short periods of high traffic, or a service restarting.

Imagine your food delivery application asks a restaurant service to confirm an order. The first request fails because the service is briefly busy. A retry may succeed once the service becomes available again.

Retries can help to:
- Recover from temporary failures.
- Make communication between services more reliable.
- Reduce the need for a user to repeat an action manually.

Retries do not fix a service that is permanently broken. They also cannot make an unsafe operation safe to repeat.

## 3. How does a retry work?

Suppose your backend allows up to 3 attempts to contact a service.

1. The backend sends the first request.
2. If the request succeeds, the backend continues normally.
3. If a temporary failure occurs, the backend waits briefly.
4. It tries again, up to the configured limit.
5. If all attempts fail, the backend stops retrying and handles the failure.

The limit of 3 attempts is only an example. The right limit depends on the operation and the system's time budget.

## 4. A real-world example

Imagine a food delivery application sending an order confirmation request to a restaurant service.

- **First attempt:** The restaurant service is temporarily unavailable.
- **Second attempt:** The backend waits briefly and tries again.
- **Success:** The restaurant service responds, so the order flow continues.
- **All attempts fail:** The backend stops trying and reports that the order could not be confirmed at this time.

The backend should not keep retrying forever. Too many attempts can waste resources and put extra pressure on an already struggling service.

## 5. When should we retry?

Retries are most useful for **temporary failures**, such as:

- A brief network interruption.
- A service temporarily being unavailable.
- A request being rejected because the service is under short-term load, when retrying is appropriate.

Retries are usually not helpful when the problem will not change by trying again. For example, repeating a request with invalid input will not make that input valid.

**Important:** Decide which failures can be retried. Do not retry every error automatically.

## 6. How long should we wait between retries?

A backend should usually wait before retrying. The waiting time can increase after each failed attempt. This is called **exponential backoff**.

For example:

- First retry: wait about 1 second.
- Second retry: wait about 2 seconds.
- Third retry: wait about 4 seconds.

These are example values, not fixed rules. Increasing the delay helps avoid sending many requests to a service that is already struggling. Systems may also add a small random delay, called **jitter**, so many clients do not retry at exactly the same time.

For a fresher interview, first understand the main idea: **use a limited number of retries and wait between attempts.**

## 7. The biggest risk: duplicate operations

A retry can be dangerous when the request creates something or changes data.

Imagine a customer places an order. The backend sends the request, and the order is created. But the response gets lost, so the backend thinks the request failed.

If the backend sends the same request again without protection, a second order might be created.

For operations such as payments and order creation, make sure repeated requests are handled safely. One common solution is **idempotency**, which helps prevent the same request from producing duplicate effects.

Read: [Idempotency](./idempotency.md)

## 8. Retry vs. Timeout

These two concepts work together, but they do different jobs:

- **Timeout:** Limits how long one attempt can wait for a response.
- **Retry:** Tries the operation again after a retryable failure or timeout.

For example, each attempt might have a 2-second timeout, with at most 3 attempts. The backend must still respect the total time allowed for the whole request.

A timeout does not prove that the original operation failed. The other service might still be processing it, so retrying must be safe.

Read: [Timeout Pattern](./timeout-pattern.md)

## 9. Common mistakes

### Retrying forever

Always set a maximum number of attempts or another clear stopping rule.

### Retrying immediately every time

Immediate repeated requests can increase pressure on an unhealthy service. Add a suitable delay between attempts.

### Retrying errors that will not change

Invalid input or other permanent errors usually need to be fixed, not retried.

### Retrying an operation that is not safe to repeat

Repeated requests can create duplicate orders or payments. Use an appropriate protection, such as idempotency, when needed.

### Ignoring the total time limit

Retries take time. Make sure the attempts and delays fit within the overall deadline for the request.

## 10. How to explain it in an interview

**Question: What is the Retry Pattern, and why do we use it?**

The Retry Pattern means trying an operation again after a temporary failure. It helps a system recover from problems such as short network interruptions or temporary service unavailability. We should limit the number of attempts, wait between retries, and retry only when it makes sense. For operations such as payments or order creation, we must also prevent duplicate effects.

**Follow-up: Should we retry every failed request?**

No. We should retry only failures that may be temporary. We should not keep retrying permanent errors, and we must make sure that repeating the request will not cause unwanted duplicate actions.

## 11. Key takeaways

- Retry means trying an operation again after a failure.
- Retries are useful for temporary failures, not every error.
- Set a maximum number of attempts and wait between retries.
- Exponential backoff increases the waiting time after repeated failures.
- Retrying can create duplicate effects if the operation is not safe to repeat.
- Timeouts limit each attempt; retries make another attempt.

## YouTube video


[ Retry Pattern Explained | Why Simple Retries Can Break Your System | System Design EP14](https://youtube.com/shorts/2RXFw2fqX_s?si=FnNSqyu185omvcGd)

## Related concepts

- [Timeout Pattern](./timeout-pattern.md)
- [Circuit Breaker](./circuit-breaker.md)
- [Idempotency](./idempotency.md)
- [Message Queues](../02-components/message-queues.md)
- [Synchronous vs. Asynchronous Communication](../03-architecture/sync-vs-async.md)
