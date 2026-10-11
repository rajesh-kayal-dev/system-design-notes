# Redis

## 1. What Is Redis?

**Redis is a fast data store that keeps data mainly in memory (RAM).** Applications often use it when they need to read or update data quickly.

Redis is commonly used for caching, but it can also help with sessions, counters, and short-lived data.

## 2. Why Do We Use Redis?

Redis can help an application to:

- Read frequently used data quickly.
- Reduce repeated work for the main database.
- Store data that expires after a short time.
- Share small pieces of data between application servers.

## 3. How Does It Work?

An application sends a command to Redis to save or read data. Redis usually handles the request from memory and returns the result quickly.

```text
Application ⇄ Redis
```

For example, an application may keep a product's details in Redis for a short time so it does not need to read the same details from the database on every request.

See [Caching](./caching.md) for the general idea of cache hits, misses, and expiration. This note focuses on Redis itself.

## 4. What Can Redis Store?

Redis supports different data types:

| Data type | Simple use |
|---|---|
| **String** | A value such as a token, number, or small text. |
| **List** | An ordered list of values. |
| **Set** | A collection of unique values. |
| **Hash** | Fields and values for one record. |
| **Sorted set** | Values kept in a ranked order. |

You do not need to memorize every type at first. Learn the ones your application needs.

## 5. Common Uses

- **Caching:** Keep frequently requested data for quick access.
- **Sessions:** Store login session information shared between servers.
- **Counters:** Count views, attempts, or other events.
- **Rate limiting:** Track how many requests a user makes in a time period.
- **Short-lived data:** Store temporary codes or data that should expire.

Related note: [Rate Limiting](./rate-limiting.md) explains how an application can limit repeated requests.

## 6. What About Expiration and Data Safety?

- **Expiration (TTL):** A key can be set to expire after a chosen time. This is useful for temporary data.
- **Persistence:** Redis can be configured to save data to disk, but this depends on its setup.
- **Memory limits:** Redis uses memory, so the team must plan how much data to keep.

Do not assume Redis is always a replacement for the main database. Important permanent data is usually stored in a database designed for that purpose.

## 7. Redis vs a Main Database

| Redis | Main database |
|---|---|
| Often used for very fast access to temporary or frequently used data. | Usually stores the application's main, long-term data. |
| Data may expire or be removed to free memory. | Data is generally expected to remain until it is changed or deleted. |
| Can reduce repeated database work. | Keeps the main record of the data. |

The right choice depends on the application's needs. Redis can be used alongside a database rather than instead of it.

## 8. When Should We Use Redis?

Use Redis when fast access to temporary or frequently used data is important and the application can handle the chosen expiration and data-loss risks.

Avoid adding Redis without a clear need. It adds another service to configure, monitor, and maintain.

## 9. Quick Revision

- Redis is a fast, memory-based data store.
- It is commonly used for caching, sessions, counters, and rate limiting.
- TTL lets data expire automatically.
- Redis can be used beside a main database; it does not have to replace it.

**Interview answer:** Redis is a fast in-memory data store often used for caching and temporary data, helping applications respond quickly and reduce repeated work on the main database.

## Related Notes

- [Caching](./caching.md)
- [Databases](../01-fundamentals/databases.md)
- [Rate Limiting](./rate-limiting.md)
- [Message Queues](./message-queues.md)
