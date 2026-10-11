# Content Delivery Network (CDN)

## 1. What Is a CDN?

**A CDN is a network of servers in different locations that delivers content to users from a nearby server.**

It is commonly used for files such as images, videos, stylesheets, and JavaScript files.

## 2. Why Do We Need It?

A CDN can:

- Load files faster for users who are far from the main server.
- Reduce repeated requests to the main server.
- Help deliver content when many users visit at once.

## 3. How Does It Work?

```text
User → Nearby CDN server → If content is missing, get it from the main server
```

1. A user requests a file.
2. The CDN checks whether it already has a saved copy.
3. If it has the file, it sends the copy to the user.
4. If the file is missing or expired, the CDN gets a fresh copy from the main server and may save it for later requests.

The main server that provides the original content is often called the **origin server**.

## 4. A Simple Example

Imagine a website with users in India, Europe, and the United States. Without a CDN, users may need to download images from one main server far away. A CDN can keep copies in different locations so users can often download those images from a nearer server.

## 5. CDN vs Application Cache

Both help avoid repeated work, but they are used in different places:

| CDN | Application cache |
|---|---|
| Delivers content from servers near users. | Keeps frequently used application data in a fast place. |
| Often serves images, videos, and website files. | Often stores data such as product details or results used by the backend. |

See [Caching](./caching.md) for the general caching idea.

## 6. What Should We Consider?

- **Fresh content:** A changed file may need to be updated or removed from the CDN cache.
- **Private data:** Personal or sensitive content needs careful caching rules.
- **Cost:** CDN usage may depend on how much data is delivered and how many requests are made.

A CDN does not automatically make every part of an application faster. It mainly helps with content it is set up to deliver.

## 7. When Should We Use a CDN?

A CDN is useful when a website or application serves many static files, videos, downloads, or other content to users in different locations.

For a small app with little public content, a CDN may not be needed immediately.

## 8. Quick Revision

- A CDN delivers content from servers located closer to users.
- It can improve loading speed and reduce work on the origin server.
- If the CDN does not have a fresh copy, it may fetch one from the origin server.
- Caching rules matter, especially for updated or private content.

**Interview answer:** A CDN is a network of distributed servers that delivers content from a nearby location, helping reduce loading time and repeated traffic to the main server.

## Related Notes

- [Caching](./caching.md)
- [Load Balancer](./load-balancer.md)
- [Networking](../01-fundamentals/networking.md)
