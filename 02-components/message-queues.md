# Message Queues

## 1. What Is a Message Queue?

A **message queue lets one part of an application send work to another part without waiting for that work to finish.** The work is stored as a message until a worker can process it.

For example, after a user places an order, the application can confirm the order first and let a worker send the email afterward.

## 2. Why Do We Need It?

A message queue can help to:

- Move slow work out of the user's request.
- Handle work when workers are busy.
- Let different parts of an application work independently.
- Retry work when something goes wrong, depending on the queue setup.

## 3. How Does It Work?

```text
Application → Queue → Worker → Result
```

1. The application creates a message describing the work.
2. The queue holds the message until a worker can take it.
3. The worker processes the task.
4. After successful processing, the message is acknowledged or marked as done, depending on the system.

The application does not need to wait for the worker to finish before responding to the user.

## 4. A Simple Example

Imagine a shopping application:

1. A user places an order.
2. The order is saved, and the application confirms it.
3. A message is added to a queue to send an order confirmation email.
4. A worker sends the email in the background.

If email sending is slow, the user does not have to wait on the order page for it to finish.

## 5. Important Parts

| Part | What it does |
|---|---|
| **Producer** | Creates and sends a message. |
| **Queue or broker** | Holds and delivers messages. |
| **Consumer or worker** | Receives a message and does the work. |
| **Acknowledgement** | Tells the queue that a message was handled. |

## 6. Message Queue vs Direct Communication

| Direct request | Message queue |
|---|---|
| The caller often waits for the other service to respond. | The sender can continue while a worker handles the task later. |
| Useful when the result is needed immediately. | Useful for background work such as emails, reports, or notifications. |
| A slow service can make the request slow. | A queue can hold work while workers catch up. |

Queues do not make work disappear. They can build up if messages arrive faster than workers can process them.

## 7. What If Something Fails?

A queue system needs a plan for failures:

- **Retry:** Try the task again when a temporary error occurs.
- **Acknowledgement:** Mark a message as completed after successful processing.
- **Dead-letter queue:** Move messages aside after repeated failures so they can be checked.
- **Duplicate handling:** A task may run more than once, so important operations should be designed to handle duplicates safely.

The exact behavior depends on the queue technology and its configuration.

## 8. Common Tools

- **RabbitMQ:** Often used to route and deliver messages between applications or workers.
- **Apache Kafka:** Often used to publish and process event streams at scale.
- **Redis:** Can support simple queue patterns or streams, but its role depends on how it is configured.

These tools are not identical. Choose based on the message volume, delivery needs, ordering, and operational requirements.

## 9. When Should We Use a Message Queue?

Queues are useful for work that can happen after the main request, such as sending emails, processing images, creating reports, or sending notifications.

Do not use a queue when the user must receive the final result immediately and a direct request is simpler.

## 10. Quick Revision

- A message queue holds work so another part of the application can process it later.
- The producer sends messages; the consumer or worker processes them.
- Queues help separate slow background work from user-facing requests.
- Plan for retries, duplicate messages, and messages that keep failing.

**Interview answer:** A message queue allows services to communicate asynchronously by storing messages until workers process them, which helps handle background tasks and reduces waiting in user requests.

## Related Notes

- [Redis](./redis.md)
- [Caching](./caching.md)
- [Architecture Patterns](../03-architecture/)
