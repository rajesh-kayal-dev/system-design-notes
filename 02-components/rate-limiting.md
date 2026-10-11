# Rate Limiting

## 1. What Is Rate Limiting?

**Rate limiting controls how many requests a user or client can make in a set time.**

For example, an application may allow a user to try logging in only a few times within one minute.

## 2. Why Do We Need It?

Rate limiting helps to:

- Protect the application from too many requests.
- Reduce misuse, such as repeated login attempts.
- Share system resources fairly between users.
- Reduce the risk of overload during heavy traffic.

## 3. How Does It Work?

The system counts requests from a user, IP address, or another chosen key. If the limit is reached, extra requests are temporarily rejected.

```text
Request → Rate Limiter → Within limit? → Application
                              └──────── No → Reject request
```

For example, if the limit is 10 requests per minute, the first 10 requests are allowed. Further requests are rejected until the limit allows them again.

## 4. Common Ways to Set a Limit

| Method | Simple meaning |
|---|---|
| **Fixed window** | Counts requests in set time periods, such as each minute. |
| **Sliding window** | Counts requests over the most recent time period. |
| **Token bucket** | Gives requests tokens over time and allows a small burst while tokens are available. |

You do not need to memorize every method at first. Understand that each method controls request volume in a slightly different way.

## 5. What Happens When the Limit Is Reached?

The application usually rejects the extra request and tells the client to try again later. HTTP commonly uses **429 Too Many Requests** for this response.

The limit should fit the action. For example, login attempts may need a stricter limit than reading a public article.

## 6. Where Does Redis Fit In?

When an application has several backend servers, they need a shared count. **Redis can store request counters and their expiration times**, so the servers can apply the same limit.

See [Redis](./redis.md) for the tool, and [Load Balancer](./load-balancer.md) for how requests can reach different servers.

## 7. When Should We Use It?

Use rate limiting for login attempts, public APIs, search requests, and other actions that could be abused or overload the system.

A limit that is too strict can block real users, so choose it based on the action and expected traffic.

## 8. Quick Revision

- Rate limiting controls how many requests are allowed in a time period.
- It helps protect services and share resources fairly.
- Extra requests may be rejected when the limit is reached.
- Redis can help share request counts across backend servers.

**Interview answer:** Rate limiting controls how often a user or client can send requests in a time period. It helps prevent misuse and protects the application from overload.

## Related Notes

- [Redis](./redis.md)
- [Load Balancer](./load-balancer.md)
- [Caching](./caching.md)
