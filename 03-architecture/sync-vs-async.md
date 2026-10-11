# Synchronous vs Asynchronous Communication

## 1. What Is Synchronous Communication?

**Synchronous communication means the caller waits for the other part to finish and return a response.** The next step usually depends on that response.

**Example:** When you log in, the app waits for the server to check your details before showing whether the login succeeded.

## 2. What Is Asynchronous Communication?

**Asynchronous communication means the caller can continue without waiting for the other part to finish the full task.** The result or update can arrive later, or the task can run in the background.

**Example:** After you place an order, the app confirms the order while a background worker sends the confirmation email.

## 3. See the Difference

```text
SYNCHRONOUS
App ── request ──> Server
App <── response ─ Server
App waits for the response.

ASYNCHRONOUS
App ── message ──> Queue ──> Worker
App continues; the worker handles the task later.
```

## 4. Main Differences

| Synchronous | Asynchronous |
|---|---|
| The caller waits for the response. | The caller can continue before the task is finished. |
| Useful when the next step needs the result now. | Useful when work can finish later. |
| A slow dependency can make the caller wait longer. | Background work can reduce waiting in the user-facing request. |
| Often uses a direct request and response. | Often uses messages, events, or background jobs. |

## 5. When Should We Use Each?

### Use synchronous communication when:
- The user needs an immediate answer, such as checking login details.
- The next step cannot continue without the result.
- A direct request and response is simple enough.

### Use asynchronous communication when:
- Work can happen after the main request, such as sending emails or notifications.
- A task may take a long time.
- The system needs to hold work until a worker is ready.

## 6. Important Things to Remember

- **Asynchronous does not automatically mean faster.** It mainly means the caller does not have to wait for the task to finish.
- The user may still need a later status or result for an asynchronous task.
- Asynchronous work needs error handling. Messages may fail or be processed more than once, depending on the system.
- Do not add queues when a simple direct request already meets the need.

## 7. Quick Revision

- **Synchronous:** Send a request and wait for its response.
- **Asynchronous:** Start or send work and continue while it is handled separately.
- **Example:** Wait for login validation; send an email in the background after an order.
- Choose based on whether the caller needs the result immediately.

**Interview answer:** Synchronous communication makes the caller wait for a response, while asynchronous communication lets the caller continue while the work is completed separately. Use synchronous communication when an immediate result is needed and asynchronous communication for background work.

### Watch this: [ Synchronous vs Asynchronous #java #springboot #rest](https://youtube.com/shorts/EKHVTmW_12g?si=ckeRk8B9lnVY865W)


## Related Notes

- [API Design](../01-fundamentals/api-design.md)
- [Message Queues](../02-components/message-queues.md)
- [Reliability](./reliability.md)
- [Monolith vs Microservices](./monolith-vs-microservices.md)
