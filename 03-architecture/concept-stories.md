# Architecture Concepts

These short explanations help you understand architecture before reading the detailed notes. The goal is to understand the idea well enough to explain it in your own words.
## What is Monolith vs Microservices and why does it matter?

> **Detailed notes:** [Monolith vs Microservices](./monolith-vs-microservices.md)

Monolith mein application ke main features ek hi application ke andar hote hain. Maan lo food delivery app ka login, orders aur payments ek hi backend application ka part hain. Microservices mein in features ko separate services mein divide kiya ja sakta hai, jahan har service apna specific kaam karti hai. Monolith start karna aur manage karna usually simple hota hai, jabki microservices teams ko services ko independently scale aur deploy karne mein help kar sakte hain. Lekin unmein communication aur operations ka complexity bhi badhta hai. Isliye chhoti application ke liye microservices automatically better choice nahi hain.

**Related concepts:** [Scalability](./scalability.md), [Synchronous vs Asynchronous](./sync-vs-async.md)

## What is Scalability and why is it needed?

> **Detailed notes:** [Scalability](./scalability.md)

Scalability ka matlab hai application ka badhte users ya workload ko handle kar paana. Maan lo pehle food delivery app ko 100 users use karte the, lekin ab 10,000 users aa rahe hain. Agar app slow hone lage, toh humein uski capacity badhane ki zaroorat ho sakti hai. Vertical scaling mein ek server ko zyada resources dete hain, aur horizontal scaling mein aur servers add karte hain. Goal sirf servers badhana nahi, balki demand ke hisaab se system ko handle karna hai.

**Related concepts:** [Load Balancer](../02-components/load-balancer.md), [Database Replication and Sharding](./database-replication-and-sharding.md)

## What is Reliability and why is it important?

> **Detailed notes:** [Reliability](./reliability.md)

Reliability ka matlab hai system ka time ke saath correctly kaam karna. Maan lo user order place karta hai, toh app ko order details sahi save karni chahiye aur galat status nahi dikhana chahiye. Reliable system sirf available rehne ki koshish nahi karta, balki expected result bhi sahi deta hai. Iske liye errors ko handle karna, important data ka backup rakhna, aur failures ko detect karna useful hota hai.

**Related concepts:** [Availability](./availability.md), [Fault Tolerance](./fault-tolerance.md)

## What is Availability and why is it needed?

> **Detailed notes:** [Availability](./availability.md)

Availability ka matlab hai jab user ko system chahiye, tab system accessible aur working ho. Maan lo shopping app par sale chal rahi hai. Agar app down ho jaaye, toh users shopping nahi kar paayenge. Healthy backup servers, monitoring aur planned recovery se downtime kam kiya ja sakta hai. Availability ka focus hai system kitni der tak usable hai; reliability ka focus hai system kitni consistently sahi kaam karta hai.

**Related concepts:** [Reliability](./reliability.md), [Fault Tolerance](./fault-tolerance.md), [Load Balancer](../02-components/load-balancer.md)

## What is Fault Tolerance and why do we need it?

> **Detailed notes:** [Fault Tolerance](./fault-tolerance.md)

Fault tolerance ka matlab hai system ka kisi component ke fail hone par bhi service continue karna ya jaldi recover karna. Maan lo ek server band ho jaata hai. Agar doosra healthy server available hai, toh traffic us par bheja ja sakta hai. Is tarah ek failure poori application ko down karne se bach sakta hai. Backup components helpful hain, lekin system ko failure detect karna aur safely switch karna bhi aana chahiye.

**Related concepts:** [Availability](./availability.md), [Reliability](./reliability.md), [Load Balancer](../02-components/load-balancer.md)

## What is Synchronous vs Asynchronous Communication?

> **Detailed notes:** [Synchronous vs Asynchronous](./sync-vs-async.md)

Synchronous communication mein ek component request bhejta hai aur response aane tak wait karta hai. Maan lo login karte waqt app ko server se login result chahiye, toh result aane tak user ko wait karna padta hai. Asynchronous communication mein component kaam bhej kar doosra kaam continue kar sakta hai. Jaise order place hone ke baad confirmation email background mein send ho sakta hai. Synchronous flow un kaamon ke liye useful hai jahan turant result chahiye, aur asynchronous flow background tasks ke liye useful hai.

**Related concepts:** [Message Queues](../02-components/message-queues.md), [API Design](../01-fundamentals/api-design.md)

## What is Stateless vs Stateful?

> **Detailed notes:** [Stateless vs Stateful](./stateless-vs-stateful.md)

Stateless system har request ko process karne ke liye request mein ya shared storage mein zaroori information dhoond sakta hai; woh kisi ek server ki local memory par session continue rakhne ke liye depend nahi karta. Isliye request kisi bhi healthy server ko di ja sakti hai, agar system ko sahi tarah design kiya gaya ho. Stateful system apni memory ya local state mein previous interaction ki information rakh sakta hai. Agar user ki state sirf ek server par hai, toh doosre server par request bhejne mein problem aa sakti hai. Shared storage ya session management is problem ko solve karne mein help kar sakte hain.

**Related concepts:** [Load Balancer](../02-components/load-balancer.md), [Redis](../02-components/redis.md)

## What is Database Replication and Sharding?

> **Detailed notes:** [Database Replication and Sharding](./database-replication-and-sharding.md)

Jab application ka data aur traffic badhta hai, toh ek database server par pressure aa sakta hai. Replication mein same data ki copies multiple database servers par rakhi jaati hain, jisse reads distribute karne aur extra copy rakhne mein help mil sakti hai. Sharding mein data ko parts mein divide karke different servers par store kiya jaata hai. Yaad rakho: replication ka matlab data ki copies banana, aur sharding ka matlab data ko parts mein baantna. Dono techniques ek hi large system mein saath bhi use ho sakti hain.

**Related concepts:** [Databases](../01-fundamentals/databases.md), [Scalability](./scalability.md), [Reliability](./reliability.md), [Database Replication and Sharding](./replication-and-sharding.md)
