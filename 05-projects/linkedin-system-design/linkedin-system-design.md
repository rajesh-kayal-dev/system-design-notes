# Designing LinkedIn Job Search

## 1. Objective

I wanted to understand what goes on behind LinkedIn's job search and how the system could handle millions of searches.

I started with a basic design and kept exploring as new problems came up. These notes capture the ideas I studied, the design decisions I made, and what I learned along the way.


## 2. Requirements

### Functional Requirements

* Job seekers can search for jobs by text and location.
* Job seekers can view a job and see similar jobs.
* Job seekers can apply for a job.
* Optional: Employers can post and update jobs.

<!-- REVIEW: "Similar jobs" is a requirement, but no section explains how it works. Check whether the API design image covers it. If not, add a note or move it out of scope. -->

**Out of scope:** feed ingestion, job alerts, employer dashboard, billing, notifications, messaging, profile, and authentication.

<!-- REVIEW: "Notifications" is out of scope, but the design sends an email when a user applies (Section 6) and uses a notification service (Section 18). Narrow the out-of-scope item (for example, "push notifications and job alerts") or note that these are a small exception. -->

### Non-Functional Requirements

* **Users:** 10 million daily users.
* **Peak traffic:** Weekdays from 7 PM to 10 PM, when traffic is about 6 times higher than usual.
* **User location:** Users are spread across different regions of India.
* **Availability over consistency:** For job search, staying available matters more than always showing the very latest data. This is the [availability vs. consistency trade-off](https://en.wikipedia.org/wiki/CAP_theorem).
* **Search freshness:** A newly posted job can take up to 2 minutes to appear in search results.

![Job search scope](./assets/job-scopes.png)

## 3. Back-of-the-Envelope Estimates

> **Overview:** Estimate how many searches happen each day, then convert that into requests per second (RPS) to see how much load the system must handle.

![Back-of-the-envelope estimates](./assets/Back-of-the-Envelope-Estimates.png)

<!-- REVIEW: The numbers are only inside this image, so readers cannot see them in the text. Consider listing the key figures here in a few lines. Section 8 uses "around 12,000 read requests per second" at peak; check that it matches the image. -->

The numbers show that job search is **read-heavy**: users search and view jobs far more often than employers post or update them.

## 4. Data Modeling

![Data model](./assets/Data-Modeling.png)

<!-- REVIEW: This section only has the image. Add a few lines in text on the main tables and how they are linked, based on the diagram. -->

## 5. API Design

> **Overview:** Design simple [REST](https://developer.mozilla.org/en-US/docs/Glossary/REST) APIs for searching jobs, viewing job details, managing job postings, and applying for jobs. They use standard [HTTP methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods), query parameters, [pagination](https://www.postgresql.org/docs/current/queries-limit.html) (returning results one page at a time), and the right [status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status).

![API design](./assets/Api-design.png)

<!-- REVIEW: This section only has the image. List the main endpoints (method and path) in text, and say how "similar jobs" is served. -->

## 6. High-Level Design

> **Overview:** A simple first design: a browser, one server, one database, and a couple of supporting services.

### Architecture Diagram

![High-Level Design](diagrams/03-high-level-design.png)

### How It Works

1. A user searches for jobs in their browser.
2. The server handles the request and gets job data from [PostgreSQL](https://www.postgresql.org/docs/current/intro-whatis.html), our main database.
3. When a user applies for a job, [Amazon SES](https://aws.amazon.com/ses/) (a managed email service) sends an email.
4. Blob storage (a place to keep files, for example [Amazon S3](https://aws.amazon.com/s3/)) stores company logos, images, and resumes.

### Trade-offs

This design is simple to build and easy to manage. PostgreSQL stores the job data, and separate services handle email and file storage. The downside is that one server and one database will struggle as more users join. We fix that step by step in the next sections.

## 7. Sequence Diagram

### Sequence Diagram

![Sequence Diagram](diagrams/04-sequence-diagram.png)

### How It Works

1. The client sends a request like `GET /jobs?q=software+engineer&page=1&limit=10`. Here, `q` is the search text, and `page` and `limit` control which results come back.
2. The server queries PostgreSQL for jobs that match the search text.
3. PostgreSQL returns the matching jobs one page at a time, using offset pagination (it skips the rows from earlier pages).
4. The server replies with `200 OK` and the job listings in JSON format.

<!-- REVIEW: The example request has a search term (`q`) but no location parameter, even though the requirements say users can search by text and location. Consider adding one, for example `&location=bengaluru`, and check that it matches the API design image. -->

### Trade-offs

This flow is easy to follow, but every search goes straight to PostgreSQL. That is fine for a small system, and the next sections show how to take that load off the database.

## 8. Scaling the Server

> **Overview:** Run several copies of the server behind a load balancer so the system can handle peak traffic.

### Architecture Diagram

![Scalability Problem](diagrams/05-scalability.png)

### How It Works

1. At peak hours, the system handles around 12,000 read requests per second. One server cannot keep up with that.
2. Instead of buying one bigger server, we run many server instances. This is called [horizontal scaling](https://en.wikipedia.org/wiki/Scalability).
3. A [load balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)) sits in front of the servers and spreads incoming requests across them.
4. Each server handles its requests and talks to the same shared PostgreSQL database.

### Trade-offs

This makes it easier to handle traffic spikes, because we can add more servers when needed. The cost is one more component to run (the load balancer). The shared database can also become a bottleneck as traffic grows.

## 9. Stateless Servers

> **Overview:** Keep servers stateless, so any server can handle any request and we can add or remove servers freely.

### Architecture Diagram

![Stateless Servers](diagrams/06-load-balancing-Stateless-Servers.png)

### How It Works

1. A [stateless](https://en.wikipedia.org/wiki/Stateless_protocol) server does not remember anything about a user between requests.
2. Because of this, two requests from the same user can safely go to different server instances.
3. Any data that must last between requests is stored outside the servers, in a shared place such as the database or a cache.
4. With no state stored inside a server, we can add or remove instances easily.

### Trade-offs

Stateless servers are easy to add, remove, or replace. The catch is that anything we need to remember between requests must live somewhere else, which means one more thing to design and look after.

## 10. Database Bottleneck and Indexing

> **Overview:** Keep job search fast as the number of jobs grows by adding a database index.

### Architecture Diagram

![Database Bottleneck and Indexing](diagrams/07-database-indexing.png)

### How It Works

1. As job postings grow, searching through millions of records gets slower.
2. Without an index, PostgreSQL has to scan the table row by row to find matching job titles. On 10 million rows, this can take around 2.1 seconds.
3. We create a [**GIN index**](https://www.postgresql.org/docs/current/gin.html) (Generalized Inverted Index) on job titles to speed up [full-text search](https://www.postgresql.org/docs/current/textsearch.html). It works like the index at the back of a book: instead of reading every page, you look up the word and jump straight to the right pages.
4. With the index, the same search on 10 million rows can take around 15 milliseconds.

<!-- REVIEW: (a) The 2.1 s and 15 ms numbers look like examples. Say whether they come from a real test or are rough estimates. (b) In PostgreSQL, a GIN index for full-text search is built on a `tsvector` column or expression, not directly on a plain text column. Confirm which one you mean. (c) The index covers job titles only, but users also search by location. Say whether location needs its own index. -->

### Trade-offs

Indexing makes searches much faster, but it uses extra disk space and makes writes a bit slower, because the index must be updated every time a job is added or changed.

## 11. Caching Hot Searches with Redis

> **Overview:** Use [Redis](https://redis.io/docs/latest/), an in-memory data store, to save popular search results so the database does less work.

### Architecture Diagram

![Caching Hot Searches with Redis](diagrams/08-redis-caching.png)

### How It Works

1. A user searches for a job. The server first checks Redis for cached results.
2. If the results are not there, the server queries PostgreSQL and saves the results in Redis.
3. When another user makes the same search, the server returns the cached results and skips the database.
4. Each cached search has a [**TTL**](https://redis.io/docs/latest/commands/expire/) (time to live) of 60 seconds. After that, the entry expires and the next search refreshes it.

<!-- REVIEW: (a) The requirements allow up to 2 minutes before a new job shows up in search. This 60-second cache delay adds to the other delays (Kafka and the indexer in Sections 18-19, plus replication lag). Confirm the total stays under 2 minutes. (b) Say what the cache key is (for example, search text + location + page), because the same search must hit the same cache entry. -->

### Trade-offs

Redis lowers the load on the database and makes repeated searches faster. The downside is that users may see slightly outdated results until the cache entry expires.

## 12. Latency and Geo-Distributed Replicas

> **Overview:** Make the application faster for users who are far from the main server by placing servers and read-only database copies closer to them.

### Architecture Diagram

![Geo-distributed replicas](diagrams/12-geo-distributed-replicas.png)

### How It Works

1. **The problem:** Say our main server is in eastern India, and a user in western India is searching for jobs. The request travels a long way, so the user waits longer.
2. **The fix:** We place servers and read-only database copies (called [read replicas](https://www.postgresql.org/docs/current/hot-standby.html)) in several locations, closer to users.
3. Searches are answered by a nearby server and a nearby database copy.
4. When someone creates or updates a job, the change goes to the main database, called the **primary database**.
5. The primary database then sends these changes to the read-only copies. This is called replication.

### Trade-offs

Users get search results faster. On the other hand, creating or updating a job can take longer, because every change must travel to the primary database.

## 13. Replication Lag and Eventual Consistency

> **Overview:** Understand why a database copy can show old data for a short time.

### Architecture Diagram

![Replication lag and database replicas](diagrams/13-replication-lag.png)

### How It Works

Suppose an employer changes a job's maximum salary from ₹5 lakh to ₹10 lakh. (A lakh is 100,000.)

1. The server saves the new salary in the primary database.
2. The employer gets a success response.
3. The database copies start receiving the new salary.
4. During this short window, a user searching through a copy may still see the old salary.
5. After a moment, every copy has the update and shows the new salary.

The delay before a copy receives the latest change is called **replication lag**. In this example, it is around 200 milliseconds.
Because all copies catch up after a short delay, we call this [**eventual consistency**](https://en.wikipedia.org/wiki/Eventual_consistency).

### Trade-offs

For job search, showing an old salary for a very short time is usually fine, and it fits our choice of availability over consistency. Systems like payment apps need to be much more careful, because showing old data there can cause real problems.

## 14. CDN for Static Assets

> **Overview:** Load company logos and images faster by keeping copies closer to users.

### Architecture Diagram

![CDN for static assets](diagrams/14-cdn-static-assets.png)

### How It Works

1. Company logos and images are stored in file storage, such as Amazon S3.
2. When a user opens a job page, their browser downloads these files. If the files are stored far away, loading takes longer.
3. A [**CDN**](https://developer.mozilla.org/en-US/docs/Glossary/CDN) (Content Delivery Network) keeps copies of these files in many locations around the world.
4. For example, when a user in India opens a job page, the CDN sends the company logo from a nearby location instead of fetching it from far away.
5. A CDN can also keep copies of static pages and other content that rarely changes.

### Trade-offs

A CDN makes images and other static content load faster. However, when a company changes its logo, some users may keep seeing the old one until the new copy reaches their location.

## 15. Recap: The Scaled System

> **Overview:** See how all the pieces work together to handle more users and respond faster.

### Architecture Diagram

![Scaled LinkedIn job search system](diagrams/15-scaled-system.png)

### How It Works

1. Job seekers and employers send requests to the application.
2. The CDN delivers images and other static files from a nearby location.
3. The load balancer spreads requests across six server instances.
4. Redis stores frequently searched job results. If the same search happens again, the server returns the saved results without querying the database.
5. If the results are not in Redis, the server gets the data from a database.
6. Read requests can go to read-only database copies, while creating or updating jobs goes to the primary database.
7. Servers and database copies in different locations help users get faster responses.

<!-- REVIEW: "Six server instances" does not appear in Section 8. Check it against the 12,000 requests-per-second estimate and the diagram (for example, how many requests per second each server would handle). -->

### Trade-offs

This design handles more users and cuts waiting time. The price is that servers, caches, and database copies spread across locations are harder to maintain.

## 16. Deep Dive: Microservices

> **Overview:** Split one large backend into smaller services, each responsible for one job.

### Architecture Diagram

![Microservices architecture](diagrams/16-microservices-architecture.png)

### How It Works

1. We can start with a single application, called a [**monolith**](https://microservices.io/patterns/monolithic.html).
2. As the number of users grows, some features get much more traffic than others. Job search, for example, gets far more requests than job applications.
3. Scaling the whole backend just for search is wasteful. Instead, we split it into three [microservices](https://microservices.io/patterns/microservices.html):
   - **Job Search Service:** Finds jobs for users. It uses Redis and read-only database copies to handle searches.
   - **Job Service:** Creates and updates job postings. It saves changes to the primary database.
   - **Application Service:** Handles users applying for jobs.
4. Each service can run on several server instances. We add instances only to the service that needs more capacity.
5. At first, the services can share the same primary database. This makes the first version easier to build, but each service should have a clear responsibility for its own data.

<!-- REVIEW: The services share one database here. That is fine as a first step, but it ties them together. Say what the end goal is (for example, one database per service). -->

### Trade-offs

Microservices let us scale each part on its own. The cost is that we now have more services to build, deploy, and manage instead of just one application.

## 17. API Gateway

> **Overview:** Use one entry point that sends each request to the right backend service.

### Architecture Diagram

![API Gateway routing requests](diagrams/17-api-gateway.png)

### How It Works

We now have separate services for searching jobs, managing jobs, and handling applications. Without help, the client would need to know which service handles which request. An [**API Gateway**](https://microservices.io/patterns/apigateway.html) removes that problem:

1. The client sends a request to the API Gateway.
2. The gateway looks at the request URL.
3. It forwards the request to the correct service.
4. The service processes the request and returns a response.

We run at least two gateway instances, so if one stops working, the other keeps handling requests.

<!-- REVIEW: It is not clear how the load balancer from Section 8 and the API Gateway fit together. For example, does a load balancer sit in front of the two gateway instances? Check the diagram and add one sentence. -->

### Trade-offs

The API Gateway gives clients one place to send requests and hides the details of the services behind it. The downside is one more component to run and manage.

## 18. Event-Driven Architecture with Kafka

> **Overview:** Let services share updates through events instead of waiting on each other.

### Architecture Diagram

![Event-driven architecture with Kafka](diagrams/18-event-driven-kafka.png)

### How It Works

Suppose an employer updates a job posting. After saving the change, the system may need to update search results, send notifications, and record data for analytics. If the job service called each of these services directly, it would have to wait for all of them.

With [**Kafka**](https://kafka.apache.org/intro), an event streaming platform, the flow is different:

1. The job service saves the job update.
2. It sends an **event** to Kafka, which is a message saying that the job has changed.
3. Other services (called consumers) listen for this event and do their own work.
4. For example, the notification service sends a notification, while the search indexer updates the search data.

The job service does not wait for these tasks to finish before replying to the employer. Kafka also keeps events while a consumer is temporarily down, so the consumer can process them when it comes back. This style of design is called [event-driven architecture](https://en.wikipedia.org/wiki/Event-driven_architecture).

<!-- REVIEW: Step 1 and step 2 are two separate actions (save to the database, then publish to Kafka). If the save works but the publish fails, the search data stays stale. Decide whether to mention this and how you would handle it (the "transactional outbox" pattern is one common approach). -->

### Trade-offs

Kafka lets services work independently and helps the system cope with busy periods. The downsides are more parts to manage, and some work finishes later instead of right away.

## 19. Search with Elasticsearch

> **Overview:** Use [Elasticsearch](https://www.elastic.co/elasticsearch), a search engine, to find jobs faster and return more relevant results.

### Architecture Diagram

![Job posting update flow](diagrams/19-job-posting-update.png)

### How It Works

PostgreSQL stores the main job data, but searching a huge number of jobs with complex search rules can get slow. Elasticsearch is built for this kind of search. For example, when a user searches for "ReactJS developer", we want relevant jobs, not only jobs that contain those exact words.

The flow looks like this:

1. An employer creates or updates a job.
2. The Job Service saves the change in PostgreSQL.
3. The Job Service sends an event to Kafka.
4. An indexer (a small service that listens for these events) receives the event and updates Elasticsearch.
5. When a user searches for a job, the Job Search Service queries Elasticsearch and returns the matching results.

PostgreSQL is still the main place where job data lives. Elasticsearch only keeps a searchable copy of it.

<!-- REVIEW: Sections 11 and 16 say the Job Search Service uses Redis and read replicas for search. Here it queries Elasticsearch. Check the diagram: does Redis still sit in front of Elasticsearch, and are read replicas now used only for other reads (like job details)? Add one sentence to say which. -->

### Trade-offs

Elasticsearch can make searches faster and the results more relevant. However, updates do not reach it instantly, so users may briefly see old search results. This is fine as long as the delay stays within our 2-minute freshness requirement.

## 20. Backend for Frontend

> **Overview:** Give the web app and the mobile app each a backend that returns exactly the data that app needs.

### Architecture Diagram

![Backend for Frontend architecture](diagrams/20-backend-for-frontend.png)

### How It Works

Our system now has several backend services, and a single screen may need data from more than one of them. For example, a job details page may need job information, company details, and the user's application status. Without a separate layer, the frontend would have to call each service and combine the results itself.

A [**Backend for Frontend**](https://learn.microsoft.com/en-us/azure/architecture/patterns/backends-for-frontends) (BFF) solves this:

- The **Web BFF** collects the data the website needs.
- The **Mobile BFF** collects the data the mobile app needs.
- Each BFF gets data from the backend services it needs and returns it to the client in one response.

We can also use [**GraphQL**](https://graphql.org/learn/) at this layer. It is a query language for APIs that lets the frontend ask for only the fields it needs, so it does not receive extra data.

<!-- REVIEW: Say where the BFF sits compared with the API Gateway (for example, client > BFF > gateway > services, or client > gateway > BFF > services). Check diagram 20. -->

### Trade-offs

A BFF keeps frontend code simpler and cuts down on extra requests and unneeded data. The cost is an additional backend layer to build and maintain for each type of client.

## 21. Reliability and Failure Handling

> **Overview:** Keep job search working even when some parts of the system fail.

### How It Works

In a real system, servers crash, databases go down, and networks drop requests. We need to handle these problems without taking down the whole application.

1. **Server failure:** If one server stops working, the load balancer sends new requests to the other healthy servers.
2. **Database failure:** If the primary database goes down, a standby database can take over, as long as [failover](https://www.postgresql.org/docs/current/warm-standby-failover.html) is set up.
3. **Cache failure:** If Redis is down, the system reads search results straight from the database. Search keeps working, but the database gets more traffic.
4. **Service failure:** If a service is briefly unavailable, the system can [retry](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/) the request a limited number of times.
5. **Consumer failure:** Events stay in Kafka while a consumer service is down, and the consumer processes them when it recovers, as long as the events are still retained.
6. **Monitoring:** We track errors, response times, and server health so we can spot problems quickly.

<!-- REVIEW: Point 5 describes a consumer being down, not Kafka itself failing (for example, a broker going down). Add a line on that if you want to cover it. Also not covered: what happens when Elasticsearch is down, and what Redis falls back to now that search uses Elasticsearch (point 3 says "the database"). -->

### Trade-offs

These measures help the system recover from failures and keep job search available. However, they add more work and more infrastructure to manage. Retries can also add extra traffic, so we should limit them.

## 22. Final Architecture Summary

> **Overview:** The final system in one place: what each part does, how a search and a job update move through it, and what is still not perfect.

<!-- REVIEW: This summary is written from the text of Sections 15-21. Check each flow below against diagrams 15 and 17-20, especially the order of the load balancer, API Gateway, and BFF. -->

### Final Architecture

- **Clients (web and mobile):** Job seekers and employers use the website or the mobile app.
- **Web BFF and Mobile BFF:** One backend for each type of client. It collects data from the services and returns it in one response.
- **CDN:** Delivers company logos, images, and other static files from a location close to the user.
- **Load balancer:** Spreads incoming requests across the server instances.
- **API Gateway:** One entry point for requests. It looks at the URL and forwards the request to the right service. We run at least two instances, so one failure does not stop it.
- **Job Search Service:** Finds jobs for users.
- **Job Service:** Creates and updates job postings and saves them in the primary database.
- **Application Service:** Handles users applying for jobs.
- **Redis:** Keeps popular search results for 60 seconds, so repeated searches are answered quickly.
- **PostgreSQL (primary and read replicas):** The main place where job data lives. Creates and updates go to the primary database. Read-only copies in several locations answer reads.
- **Kafka:** Carries events, such as "this job changed", from the Job Service to other services.
- **Search indexer:** Listens to these events and updates Elasticsearch.
- **Elasticsearch:** A searchable copy of the job data, used to find relevant jobs.
- **Notification service:** Listens to events and sends notifications.
- **Amazon SES and Amazon S3:** SES sends the email when a user applies. S3 stores logos, images, and resumes.

The servers are stateless, so any instance can handle any request.

### Job Search Request Flow

Say a user searches for "ReactJS developer".

1. The app sends a request like `GET /jobs?q=reactjs+developer&page=1&limit=10`.
2. The load balancer sends it to an available instance. The API Gateway looks at the URL and forwards it to the Job Search Service. The BFF of the web or mobile app is also part of this path.
3. The Job Search Service checks Redis for a saved result of the same search. If it finds one, it returns it right away.
4. If not, it queries Elasticsearch, which returns the matching jobs, one page at a time. The service saves the result in Redis with a 60-second TTL.
5. The response goes back to the user as `200 OK` with the job listings in JSON.
6. Company logos and images load separately from the CDN.

<!-- REVIEW: Steps 2-4 mix what Sections 11, 16, 17, 19, and 20 say. Confirm the order and whether Redis is still used in front of Elasticsearch (see the note in Section 19). -->

### Job Creation or Update Flow

1. An employer creates or updates a job in the app. The request reaches the Job Service through the load balancer and the API Gateway.
2. The Job Service saves the change in the primary PostgreSQL database.
3. The Job Service sends an event to Kafka and replies to the employer. It does not wait for the other services.
4. The search indexer reads the event and updates Elasticsearch. The notification service reads the same event and does its own work.
5. The primary database sends the change to the read replicas. For a short time, a replica can still show the old data.
6. Cached searches in Redis expire after at most 60 seconds, so the next search gets the new data.

Steps 4 to 6 happen after the employer gets a reply, so a new job takes a little time to show up in search. Our requirement allows up to 2 minutes.

### How the System Handles Growth

- **Load balancer and stateless servers:** At peak hours, we run more server instances and the load balancer shares the requests between them.
- **Redis:** Popular searches are answered from memory, so the database and Elasticsearch get fewer requests.
- **Index and Elasticsearch:** Searching stays fast as the number of jobs grows.
- **Read replicas in several locations:** Reads are spread across many database copies, and users get answers from a nearby location.
- **CDN:** Images and static files are delivered from nearby, and our servers do not have to send them.
- **Separate services:** Job search gets far more traffic than applications, so we add instances only to the Job Search Service.
- **Kafka:** The Job Service does not wait for indexing or notifications. Other services catch up on their own, even after a short outage.

### Remaining Trade-offs

- **More parts to run.** We now have a load balancer, API Gateway, BFFs, three services, Redis, Kafka, an indexer, Elasticsearch, and read replicas. Each one needs deployment and monitoring.
- **Data can be slightly old.** The cache, replication lag, and the indexer each add a small delay. This is fine for job search because we chose availability over consistency, but the total delay must stay within 2 minutes.
- **Two copies of job data.** Elasticsearch is a copy of what is in PostgreSQL. If an event is lost or the indexer fails, the two can differ.
- **Writes still go to one primary database.** For users far from it, creating or updating a job can take longer.
- **Shared database between services.** In the first version, the services share one database, which keeps them tied together.

## 23. Research References

> **Overview:** The sources behind the design decisions in this document.

### Course

- Course name, link, and transcript: <!-- REVIEW: Add the name and link of the course you followed. They are not in the file. -->

### Official Documentation and Other Links Used in This Document

**PostgreSQL** (data model, pagination, indexing, read replicas, failover)

- [What is PostgreSQL?](https://www.postgresql.org/docs/current/intro-whatis.html)
- [Constraints and foreign keys](https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-FK)
- [LIMIT and OFFSET](https://www.postgresql.org/docs/current/queries-limit.html)
- [Full text search](https://www.postgresql.org/docs/current/textsearch.html)
- [GIN indexes](https://www.postgresql.org/docs/current/gin.html)
- [Hot standby (read replicas)](https://www.postgresql.org/docs/current/hot-standby.html)
- [Failover](https://www.postgresql.org/docs/current/warm-standby-failover.html)

**Redis** (caching and TTL)

- [Redis documentation](https://redis.io/docs/latest/)
- [EXPIRE command](https://redis.io/docs/latest/commands/expire/)

**APIs and the web**

- [REST (MDN)](https://developer.mozilla.org/en-US/docs/Glossary/REST)
- [HTTP methods (MDN)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [HTTP status codes (MDN)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [CDN (MDN)](https://developer.mozilla.org/en-US/docs/Glossary/CDN)
- [GraphQL](https://graphql.org/learn/)

**Architecture patterns and tools**

- [Monolithic architecture (microservices.io)](https://microservices.io/patterns/monolithic.html)
- [Microservices (microservices.io)](https://microservices.io/patterns/microservices.html)
- [API Gateway (microservices.io)](https://microservices.io/patterns/apigateway.html)
- [Backends for Frontends (Microsoft)](https://learn.microsoft.com/en-us/azure/architecture/patterns/backends-for-frontends)
- [Apache Kafka introduction](https://kafka.apache.org/intro)
- [Elasticsearch](https://www.elastic.co/elasticsearch)
- [Amazon S3](https://aws.amazon.com/s3/)
- [Amazon SES](https://aws.amazon.com/ses/)
- [Timeouts, retries, and backoff with jitter (AWS)](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/)

**Background concepts**

- [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem)
- [Scalability](https://en.wikipedia.org/wiki/Scalability)
- [Load balancing](https://en.wikipedia.org/wiki/Load_balancing_(computing))
- [Stateless protocol](https://en.wikipedia.org/wiki/Stateless_protocol)
- [Eventual consistency](https://en.wikipedia.org/wiki/Eventual_consistency)
- [Event-driven architecture](https://en.wikipedia.org/wiki/Event-driven_architecture)

**Thank You!..😊**