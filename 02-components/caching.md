# Caching

## 1. What Is Caching?

**Caching means keeping a copy of frequently used data in a faster place so it can be accessed more quickly next time.**

For example, a shop may keep its most popular items near the counter instead of going to the storeroom every time someone asks for them.

## 2. Why Do We Need It?

Without a cache, the application may need to read the same data from the database again and again.

Caching can:

- Make responses faster.
- Reduce repeated database work.
- Help the system handle more requests.
- Reduce database load and cost.

## 3. How Does It Work?

When the application needs some data:

1. It checks the cache first.
2. If the data is there, it returns the cached data. This is a **cache hit**.
3. If the data is missing, it gets the data from the database.
4. It stores a copy in the cache so the next request may be faster. This is a **cache miss**.

```text
Request → Cache
            ├─ Data found → Return data
            └─ Not found → Database → Save a copy in cache
```

## 4. A Simple Example

Imagine a shopping app that shows product details to many users.

- The first request gets the product details from the database and saves a copy in the cache.
- Later requests can get those details from the cache instead of querying the database every time.

The cache is useful because the same information is requested often.

## 5. Where Can Caching Be Used?

| Type | What it stores |
|---|---|
| **Browser cache** | Files such as images, styles, and scripts on the user's device. |
| **CDN cache** | Copies of content closer to users. |
| **Application cache** | Frequently used application data, often in memory or a tool such as Redis. |
| **Database cache** | Data or query results kept to reduce repeated database work. |

## 6. What About Old Data?

Cached data can become outdated when the original data changes. The application needs a plan to keep it reasonably fresh.

Two common approaches are:

- **Expiration time (TTL):** The cached item expires after a set time.
- **Cache update or removal:** When data changes, update the cached copy or remove it so fresh data can be loaded.

There is a trade-off: a longer cache time can improve speed, but users may see old data for longer.

## 7. Benefits and Limitations

**Benefits**

- Faster responses for repeated requests.
- Fewer reads from the database.
- Better handling of high traffic.

**Limitations**

- Cached data can become outdated.
- The team must decide when data expires or gets updated.
- Caching adds extra logic and should not be used for every type of data.

## 8. When Should We Use It?

Caching is useful when data is read often and does not change every moment, such as product details, public profiles, or popular articles.

Be more careful when data changes frequently or must always be up to date, such as account balances.

## 9. Quick Revision

- A cache keeps frequently used data in a faster place.
- A **cache hit** means the data was found in the cache.
- A **cache miss** means the data was not found, so the application gets it from another source.
- TTL sets how long cached data can stay before it expires.
- Caching improves speed, but the application must handle outdated data.

**Interview answer:** Caching stores frequently used data in a faster location so future requests can be served more quickly and the database does less repeated work.

## Related Notes

- [Load Balancer](./load-balancer.md)
- [Databases](../01-fundamentals/databases.md)
