# Timeout Pattern

## 1. What is the Timeout Pattern?

The **Timeout Pattern** means setting a maximum amount of time that an application will wait for an operation to finish.

For example, when you log in to a website, the backend may need to check your details in a database. Usually, this happens quickly. But sometimes the database or another service is slow or unavailable.

Without a timeout, the application might keep waiting for a very long time. With a timeout, it stops waiting after a defined limit and handles the situation.

**In simple words:** A timeout is a time limit for waiting for a response.

A timeout does not mean that every request must finish within exactly the same time. It means that the application has decided how long it is willing to wait for a particular operation.

## 2. Why do we need timeouts?

Imagine that your food delivery application calls a payment service to confirm a payment.

1. The customer clicks **Pay**.
2. Your backend sends a request to the payment service.
3. The payment service becomes slow and does not respond.
4. Your backend keeps waiting.

If the backend waits forever, the request may keep using server resources. Many requests waiting at the same time can consume available connections, memory, or worker capacity. Other customers may then experience slow responses too.

A timeout limits how long your backend waits. When the time limit is reached, your backend can stop waiting and return an appropriate response or follow a recovery process.

Timeouts help to:
- Avoid waiting indefinitely for a slow dependency.
- Free up resources sooner when a request cannot complete in time.
- Keep response times within a reasonable limit.
- Reduce the risk that slow dependencies cause a wider slowdown.

A timeout does **not** make the other service faster or guarantee that the operation succeeds. It limits the waiting time for the caller.

## 3. How does a timeout work?

Suppose your backend allows up to 3 seconds for a request to an external service.

1. Your backend sends the request.
2. A timer starts.
3. If the service responds within 3 seconds, your backend receives the response and continues.
4. If no response arrives before 3 seconds, the timeout is reached.
5. Your backend stops waiting for the response and handles the timeout, for example by returning an error or starting a safe recovery process.

```text
Client
  |
  v
Backend
  |
  | Send request and start timer
  v
External service
  |
  |-- Response arrives within 3 seconds --> Continue
  |
  |-- No response after 3 seconds -------> Timeout
                                           |
                                           v
                                  Handle the failure
```

The 3-second value is only an example. The correct timeout depends on the operation and the service being called.

## 4. A real-world example

Imagine you order food through a delivery application.

The backend sends a request to a restaurant service to check whether the restaurant is accepting orders. The backend waits up to 2 seconds for a response.

- **Response in 500 milliseconds:** The restaurant service responds in time, so the order flow continues.
- **No response after 2 seconds:** The backend reaches the timeout and can tell the customer that the restaurant could not be checked right now.

The application should not tell the customer that the restaurant definitely rejected the order. The service might simply be slow, and the backend did not receive a response in time.

## 5. Important detail: a timeout does not always cancel the operation

This is an important concept to understand.

Suppose your backend sends a request to another service. Your backend waits for 3 seconds, times out, and stops waiting. The other service might still be processing the request.

For example, a payment request might reach the payment provider, but the response may take too long to return. Your backend timing out does **not** prove that the payment failed.

If the backend immediately sends the payment request again, the customer could potentially be charged twice unless the payment flow safely handles duplicate requests.

For operations that create changes, such as payments or orders, the system needs a way to determine the final result or safely repeat the operation. **Idempotency** is one useful concept here.

Read: [Idempotency](./idempotency.md)

## 6. Where should we use timeouts?

Timeouts are especially useful when one part of an application waits for another part to respond.

Common examples include:

- **API calls:** Set a limit on how long your backend waits for another API.
- **Database queries:** Avoid letting a query wait indefinitely.
- **Microservice communication:** Limit the time one service waits for another service.
- **Message processing:** Set appropriate limits for operations that wait for a response or acknowledgement.
- **External providers:** Avoid waiting indefinitely for payment, email, maps, or other third-party services.

The exact timeout should match the operation. A quick database lookup and a report that takes longer to generate should not necessarily have the same limit.

## 7. What happens after a timeout?

A timeout tells the application that the operation did not finish within the allowed time. The application still needs to decide what to do next.

Depending on the operation, it might:

- Return a clear error to the client.
- Show a message asking the user to try again later.
- Retry the request if retrying is safe and appropriate.
- Use a fallback response if one is available.
- Record the timeout in logs or monitoring so the team can investigate.

Do not automatically retry every timed-out request. First consider whether the operation is safe to repeat and whether the service may still be processing the original request.

Related notes:
- [Retry Pattern](./retry-pattern.md)
- [Circuit Breaker](./circuit-breaker.md)

## 8. How do we choose a timeout value?

There is no single timeout value that works for every operation.

Consider these points:

- **Expected response time:** How long does this operation normally take?
- **User experience:** How long can the user reasonably wait?
- **End-to-end deadline:** How much total time is available for the complete request?
- **Downstream services:** If several services are called one after another, each call must fit within the overall time budget.

For example, if a request has a total time budget of 5 seconds and the backend calls three services one after another, allowing each service to take 5 seconds could make the full request take much longer than 5 seconds. The timeouts need to fit within the overall deadline.

Use real response-time measurements and testing to choose sensible values. Do not select a timeout randomly and assume it is correct.

## 9. Common mistakes

### Mistake 1: Waiting forever

Without a time limit, slow dependencies can leave requests waiting and using resources for too long.

### Mistake 2: Setting the timeout too low

If the timeout is shorter than the normal response time, healthy requests may fail unnecessarily.

### Mistake 3: Setting the timeout too high

If the timeout is too high, users and backend resources may still be stuck waiting for a long time.

### Mistake 4: Assuming a timeout means the operation failed

A caller may not receive a response even though the other service completed the operation. This matters especially for payments, orders, and other actions that change data.

### Mistake 5: Retrying without checking safety

Retrying a request that creates an order or charges a payment may cause duplicate effects unless the operation is designed to handle repeated requests safely.

## 10. How to explain it in an interview

**Question: What is a timeout, and why do we need it?**

A timeout sets the maximum time an application waits for a response from another service or operation. We use it to prevent requests from waiting indefinitely and consuming resources when a dependency is slow. If the time limit is reached, the application handles the situation, for example by returning an error or using a safe recovery process. A timeout does not always mean the operation failed, because the other service may still be processing it.

**Follow-up: What is the difference between a timeout and a retry?**

A timeout limits how long we wait for one attempt. A retry means trying the operation again after a failure or timeout. They can work together, but retries need limits and should only be used when repeating the operation is safe.

## 11. Key takeaways

- A timeout is a maximum waiting time for an operation.
- It protects the caller from waiting indefinitely.
- The timeout value depends on the operation and the overall request deadline.
- A timeout does not guarantee that the operation stopped or failed.
- Retrying after a timeout must be done carefully, especially for payments and orders.
- Timeouts are commonly used with retries and circuit breakers, but each solves a different problem.


## YouTube video


[Timeout Pattern Explained | Why Systems Should Not Wait Forever | System Design EP16](https://youtube.com/shorts/KeEXYq2ZeZU?si=vr754y0aRrKv45Rx)

## Related concepts

- [Retry Pattern](./retry-pattern.md)
- [Circuit Breaker](./circuit-breaker.md)
- [Idempotency](./idempotency.md)
- [Synchronous vs. Asynchronous Communication](../03-architecture/sync-vs-async.md)
- [Message Queues](../02-components/message-queues.md)
