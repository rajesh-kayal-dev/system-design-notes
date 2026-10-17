# System Design Interview Framework

A step-by-step framework for answering system design interview questions.

> **Main idea:** Requirements first, design later. Don't start drawing load balancers, Redis, databases, or queues before understanding what the system needs to do and why.

## 1. Understand the Requirements

Before drawing the architecture, discuss the requirements with the interviewer. Even if you've practised the question before, ask first. The interviewer wants to understand your thought process, how you clarify a problem, and why you choose a solution.

### 1.1 Functional Requirements

Functional requirements answer: **What should the system do?**

For a URL Shortener, possible requirements are:

- **Create a short URL:** User provides a long URL and gets a short URL.
- **Redirect to the original URL:** When someone opens the short URL, send them to the original URL.
- **URL expiry:** Decide how long a short URL should stay active, for example, seven days, one month, or one year.
- **Analytics:** Track clicks, country, device, and time if required.
- **Authentication and URL management:** Decide whether users need accounts and whether they can view, delete, or manage their own URLs. Clarify whether users have a limit on how many URLs they can create.

Not every design needs every feature. Ask the interviewer which features are required. Don't build analytics or authentication if they are not needed. If you make an assumption, tell the interviewer.

### 1.2 Non-Functional Requirements

Non-functional requirements answer: **How well should the system work?**

| Requirement | Simple meaning | URL Shortener example |
|---|---|---|
| Scalability | Handle more users and requests as the system grows. | Many users create links and redirect through them. |
| Availability | Users can access the system when they need it. | The service should continue responding rather than being completely down. |
| Latency | How long a request takes. Lower is usually better. | Creating or opening a short URL should be fast. |
| Reliability | The system returns the correct result. | A short URL always maps to the correct original URL. |
| Security | Protect users and their data. | One signed-in user must not manage another user's URLs. |
| Durability | Saved data should not disappear unexpectedly. | A saved URL mapping should remain stored. |
| Consistency | Users should see results that match recent changes where required. | A deleted URL should not continue working because an old copy is still being served. |

You do not need to optimise every quality equally. Ask which requirements matter most and prioritise them.

## 2. Estimate Capacity

Capacity estimation gives a rough idea of how much traffic and storage the system may need. Start with estimates and state your assumptions.

### 2.1 Estimate users and requests

Consider:

- **Total users:** How many people have accounts or use the service?
- **Daily Active Users (DAU):** How many actually use the system each day? Total users do not all use it at once.
- **Writes per second:** How many short URLs are created each second?
- **Read-to-write ratio:** How many times are short URLs opened compared with how many are created?
- **Peak traffic:** Traffic changes throughout the day and may rise during campaigns or other busy periods.

### Example

Assumptions:

- 100 million total users.
- 10% are active each day, so 10 million Daily Active Users.
- Each active user creates one short URL per day.
- One day has 86,400 seconds.

Approximate write rate:

`10,000,000 ÷ 86,400 ≈ 116 writes/second`

Assume a read-to-write ratio of **10:1**:

`116 × 10 ≈ 1,160 reads/second`

For an example peak estimate **5× average traffic**:

`1,160 × 5 ≈ 5,800 reads/second`

These are example estimates, not fixed rules. Use the interviewer’s numbers when provided and make your own assumptions clear.

### 2.2 Estimate storage

First decide what one URL record stores. For example:

- ID
- Original URL
- Short URL or short code
- User ID, if accounts are supported
- Created time
- Expiry time

Estimate the size of one record, then multiply it by the number of records created per day, month, or year.

example assumptions:

- Limit long URLs to 500 characters.
- Estimate one record at approximately 1 KB.
- Create 10 million URL records per day.

Example daily storage estimate: `10 million × 1 KB`.

Then extend the estimate to a month or year. Actual storage depends on the database format, indexes, metadata, and overhead, so treat this as a rough estimate.

## 3. Define the Main APIs

API design explains how the client communicates with the system. Define a few main APIs to show the input and output pattern; you do not need to list every possible endpoint.

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/urls` | Send a long URL in the request body and receive a short URL. |
| `GET` | `/{shortCode}` | Find the original URL for a short code and redirect to it. |
| `GET` | `/{shortCode}/analytics` | Return click analytics if analytics are part of the requirements. |

The exact endpoint format can vary. The important part is to explain what the client sends and what the system returns.

## 4. Define the Data Model and Access Patterns

Before choosing a database technology, first work out what data needs to be stored and how the system will use it. Then choose a database that fits the requirements and explain why.

### 4.1 Example URL record

| Field | Purpose |
|---|---|
| `id` | Identifies the record. |
| `originalUrl` | The original long URL. |
| `shortCode` | The short code used to look up the original URL. |
| `userId` | The owner, if accounts are supported. |
| `createdAt` | When the short URL was created. |
| `expiresAt` | When the short URL expires, if expiry is supported. |

### 4.2 Understand access patterns

Ask these questions before choosing the database and indexes:

- **What do we read?** The original URL is looked up using the short code.
- **What do we write?** A new mapping is stored when a short URL is created.
- **How often do we read and write?** The example system is read-heavy because a short URL may be opened many times after it is created once.
- **Which key do we query by?** The short code is the main key used for redirects, so consider indexing it.

Access patterns help guide database and index choices.

## 5. Draw the High-Level Design

Start with the simplest architecture that meets the requirements. Add components only when there is a reason to add them.

### 5.1 Basic request flow

1. User sends a request.
2. DNS directs the request toward the service.
3. A load balancer sends it to an available application server.
4. The application server handles the request.
5. For a write, it stores the URL mapping in the database.
6. For a read, it looks up the mapping and returns a redirect to the user.

### 5.2 Add a cache when repeated reads need it

The example URL Shortener is read-heavy. Many people may open the same short URL, so repeatedly reading the same mapping from the database can add load and latency.

A cache such as Redis can reduce repeated database reads:

1. Application server checks the cache using the short code.
2. **Cache hit:** The mapping is found, so use it.
3. **Cache miss:** Read the mapping from the database, store a copy in the cache, and return the result.
4. Next time, the same mapping may be served directly from the cache.

Explain **why** you add each component. A queue may be useful for work that can happen in the background; do not add one without a requirement for asynchronous processing.

## 6. Deep Dive into the Most Challenging Part

Do not explain every component in great detail. Identify the hardest or most important part of the system and discuss it with the interviewer.

For the URL Shortener, the core problem is generating a short code from a long URL.

### 6.1 Random string

Generate a random string from letters and numbers.

- **Benefit:** Simple approach.
- **Problem / trade-off:** Two different URLs could get the same code. The system needs a way to detect and handle collisions.

### 6.2 Hashing

Apply a hash function to the long URL and use part of the result as the short code.

- **Benefit:** A repeatable way to generate a value from the input.
- **Problem / trade-off:** Different URLs can produce the same short code, so collisions must be handled.

### 6.3 Counter or ID-based approach

Assign each new URL a unique number and turn that number into a short code.

- **Benefit:** A unique ID can avoid random collisions if IDs are assigned safely.
- **Problem / trade-off:** With multiple application servers, decide how unique IDs are generated. Independent counters on each server may conflict; a shared or distributed ID strategy adds complexity.

For each approach, explain its benefits, possible problems, and how you would handle them. The best choice depends on the requirements.

## 7. Find Bottlenecks and Improve the Design

Challenge your own design. Ask what might stop working well as traffic or data grows.

| Possible bottleneck | Possible improvement | What to discuss |
|---|---|---|
| One application server cannot handle the traffic. | Run multiple application servers behind a load balancer. | How traffic is spread across servers. |
| One load balancer becomes unavailable. | Use a highly available load-balancer setup, such as active-active or active-passive. | How requests reach a healthy load balancer; DNS routing may be part of the design. |
| The database cannot handle the load. | Consider replication, partitioning, or sharding when needed. | Which part of the workload is the problem and what each option helps with. |
| Repeated reads overload the database. | Add caching. | Cache invalidation, stale values, memory cost, and eviction policy. |
| Non-critical work delays the user response. | Move the work to a message queue and process it in the background. | The user can get a response sooner, but the work completes later. |

These changes should come from the bottleneck and the requirements, not from a desire to add more technologies.

## 8. Discuss Trade-offs

No design is perfect. When you add a component, explain what it improves and what new problems it creates.

### 8.1 Cache / Redis

| Benefits | Costs or challenges |
|---|---|
| Lower latency for repeated reads. | Cached data can become stale. |
| Reduces database read load. | Cache invalidation adds work and complexity. |
| Can serve frequently used values quickly. | Memory and infrastructure cost; an eviction policy is needed. |

### 8.2 Database replication

| Benefits | Costs or challenges |
|---|---|
| Improves availability when configured for failover. | Replication lag may mean replicas have older data. |
| Read queries can be spread to replicas. | Consistency and failover need careful handling. |

### 8.3 Message queue

| Benefits | Costs or challenges |
|---|---|
| Allows background, asynchronous processing. | Messages may be processed more than once. |
| Reduces direct dependency between services. | Retries, ordering, and handling repeated failures need a plan. |
| Buffers work while a consumer is busy. | A growing backlog must be monitored and processed. |

The goal is not to make every component perfect. It is to explain the trade-offs that matter for this system.

## 9. Common Interview Mistakes

1. **Skipping requirements:** Starting the diagram before agreeing on what the system must do.
2. **Adding technology too early:** Choosing Kafka, Redis, or a particular database before understanding the requirements and access patterns.
3. **Ignoring trade-offs:** Presenting the design as perfect instead of explaining its limits and how you would handle them.

## 10. Complete Framework

Use this order as a guide during a system design interview:

1. **Clarify requirements** — functional and non-functional.
2. **Prioritise requirements** — decide what is in scope and what matters most.
3. **Estimate capacity** — users, daily active users, requests per second, read/write ratio, peak traffic, and storage.
4. **Define main APIs** — show how the client interacts with the system.
5. **Define the data model and access patterns** — what to store, read, write, and query by.
6. **Draw the high-level design** — start simple and explain each component.
7. **Deep dive** — focus on the most challenging part and discuss possible solutions.
8. **Find bottlenecks** — consider growth, failure, and performance problems.
9. **Discuss trade-offs** — explain both benefits and costs of the chosen components.
10. **Present the final design** — summarise how it meets the prioritised requirements.

> This is a guide, not a fixed script. Interviews are discussion-based, so the order may change as the interviewer asks follow-up questions.

## Quick Revision

- **Requirements first:** Never jump straight into architecture.
- **Functional:** What the system does.
- **Non-functional:** How well it should work.
- **Capacity:** Estimate the load before deciding how much infrastructure is needed.
- **APIs and access patterns:** Define how clients use the service and how data is read or written.
- **High-level design:** Start simple; add components for a reason.
- **Deep dive:** Focus on the core challenge.
- **Bottlenecks:** Identify what could limit performance or availability.
- **Trade-offs:** Every added component brings benefits and costs.

## Video Reference

[System Design Interview Framework | How to Approach Any HLD Question | EP 10](https://www.youtube.com/watch?v=yxjLoe2TbDY)
