# Component Concepts in Simple Words

These short explanations help you understand each concept before reading the detailed notes. The goal is to understand the idea well enough to explain it in your own words.

## What is a Load Balancer and why is it used?

> **Detailed notes:** [Load Balancer](./load-balancer.md)

Load balancer incoming requests ko multiple servers mein distribute karta hai. Maan lo ek application par bahut saare users aa rahe hain. Agar saare requests ek hi server par jayenge, toh woh slow ya overloaded ho sakta hai. Load balancer requests ko available servers par bhejta hai, aur agar koi server fail ho jaye toh healthy servers ko use kar sakta hai.

**Related concepts:** [Caching](./caching.md), [Rate Limiting](./rate-limiting.md)

## What is Caching and why is it used?

> **Detailed notes:** [Caching](./caching.md)

Caching ka matlab frequently used data ki ek copy fast place par rakhna hai, taaki next time data jaldi mil sake. Maan lo shopping app mein same product details baar-baar dekhi ja rahi hain. Har baar database se data laane ke bajaye, app cached copy use kar sakti hai. Isse response fast hota hai aur database par load kam hota hai. Cache ka data kabhi old ho sakta hai, isliye usko update ya expire karne ka plan bhi chahiye.

**Related concepts:** [Redis](./redis.md), [CDN](./cdn.md), [Databases](../01-fundamentals/databases.md)

## What is Redis and why is it used?

> **Detailed notes:** [Redis](./redis.md)

Redis ek fast in-memory data store hai jo data ko mainly RAM mein rakhta hai. Isliye kai use cases mein data bahut quickly read ya update ho sakta hai. Redis caching ke liye common hai, lekin sessions, counters, temporary data aur rate limiting mein bhi use hota hai. Caching ek technique hai; Redis us technique ko implement karne ke liye ek tool ho sakta hai.

**Related concepts:** [Caching](./caching.md), [Rate Limiting](./rate-limiting.md), [Databases](../01-fundamentals/databases.md)

## What is a Message Queue and why is it used?

> **Detailed notes:** [Message Queues](./message-queues.md)

Message queue ek part of application ka kaam doosre part tak pahunchati hai, bina uska result aane tak wait karwaye. Maan lo user ne food order kiya. App order save karke user ko confirmation de sakti hai, jabki email ya notification background mein worker send karta hai. Isse user ko unnecessary wait nahi karna padta. Queue messages ko hold karti hai jab tak worker unhe process na kare.

**Related concepts:** [Load Balancer](./load-balancer.md)

## What is Rate Limiting and why is it needed?

> **Detailed notes:** [Rate Limiting](./rate-limiting.md)

Rate limiting ek user ya client ke requests ko fixed time period mein limit karta hai. Maan lo login page par koi baar-baar password try kar raha hai. Rate limiter attempts count karta hai aur limit cross hone par extra requests ko temporarily reject kar sakta hai. Isse misuse kam hota hai aur application ke resources better protect hote hain. Redis multiple backend servers ke beech request counts share karne mein help kar sakta hai.

**Related concepts:** [Redis](./redis.md), [Load Balancer](./load-balancer.md)

## What is a CDN and why is it used?

> **Detailed notes:** [CDN](./cdn.md)

CDN means Content Delivery Network, duniya ke different locations par servers ka network hota hai, jo content users ko nearby location se deliver karta hai. Maan lo website ki images ek hi main server par stored hain aur users alag-alag countries mein hain. CDN images ki copies users ke closer servers par rakh sakta hai, isliye images faster load ho sakti hain aur main server par repeated traffic kam hota hai. CDN mostly images, videos aur website files jaise content ke liye useful hai.

**Related concepts:** [Caching](./caching.md), [Networking](../01-fundamentals/networking.md)
