# Load Balancer

## 1. What Is a Load Balancer?

A **load balancer** receives requests from users and distributes them across multiple servers.

Think of it like a receptionist who sends each customer to an available counter instead of making everyone wait in one line.

## 2. Why Do We Need It?

When many users visit an application, one server may become slow or overloaded.

A load balancer helps to:

- Share requests across servers.
- Reduce pressure on a single server.
- Keep the application available if a server fails.
- Handle more users by adding servers.

## 3. How Does It Work?

1. A user sends a request to the application.
2. The load balancer receives the request.
3. It chooses a suitable server.
4. That server processes the request and sends back the response.

```text
                 ┌── Server 1
User → Load Balancer ── Server 2
                 └── Server 3
```

The load balancer helps decide where each request should go. The servers do the actual application work.

## 4. Common Ways to Choose a Server

| Method | Simple meaning |
|---|---|
| **Round Robin** | Sends each new request to the next server in order. |
| **Least Connections** | Sends a request to the server handling fewer active connections. |
| **IP Hash** | Uses the user's IP address to help choose a server consistently. |

Different methods work better for different applications.

## 5. What If a Server Fails?

A load balancer can check whether servers are healthy. If a server stops responding, it can stop sending new requests to that server and use the healthy ones instead.

This helps the application keep working, as long as enough healthy capacity remains.

## 6. Benefits and Limitations

**Benefits**

- Shares traffic across servers.
- Improves availability.
- Makes it easier to add more servers.

**Limitations**

- Adds another component to manage.
- Cannot fix every performance problem, such as a slow database.
- The load-balancing setup itself must be designed to avoid becoming a single point of failure.

## 7. When Should We Use It?

Use a load balancer when one server is no longer enough or when the application needs to keep serving users if a server fails.

For a small application with low traffic, one server may be enough at first.

## 8. Quick Revision

- A load balancer distributes requests across servers.
- It helps with traffic, availability, and scaling.
- It can avoid servers that fail health checks.
- It does not automatically solve problems in the database or application code.

**Interview answer:** A load balancer distributes incoming requests across multiple servers to share traffic, improve availability, and help an application handle more users.

## Related Notes

- [Caching](./caching.md)
- [System Design Basics](../01-fundamentals/system-design-basics.md)
