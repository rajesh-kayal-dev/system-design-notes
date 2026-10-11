# Database Replication and Sharding

## 1. Why do we need them?

As an application grows, one database server may struggle to handle all reads, writes, or stored data.

Two common ways to handle this are:

- **Replication:** Keep copies of the same data on multiple database servers.
- **Sharding:** Split data across multiple database servers.

They solve different problems.

## 2. Database Replication

Replication means copying data from one database server to another.

A common setup has a **primary** database that handles writes and one or more **replicas** that can serve reads.

```text
              Write
                |
                v
          [Primary DB]
           /        \\
          v          v
     [Replica]   [Replica]
       Reads       Reads
```

### Why use replication?

- Share read traffic across replicas.
- Keep another copy of the data.
- Help the system continue serving reads if a replica fails.

### Important point

Replication can have a delay. A recent write may reach a replica a little later, so a read from that replica might briefly return older data.

## 3. Database Sharding

Sharding means splitting a large dataset into smaller parts and storing each part on a different database server.

For example, users could be split by user ID:

- Shard 1 stores users with IDs in one range.
- Shard 2 stores users with IDs in another range.
- Shard 3 stores another range.

Each shard stores only part of the full dataset.

### Why use sharding?

- Store more data than one database server can comfortably handle.
- Spread read and write work across servers.
- Increase capacity as the application grows.

### Important point

The system needs a **shard key** to decide where each record belongs. A poor shard key can overload one shard or make data harder to find.

## 4. Replication vs. Sharding

| | Replication | Sharding |
|---|---|---|
| What happens? | Copies the same data | Splits data into parts |
| Main goal | More read capacity and extra copies | More storage and read/write capacity |
| Data on each server | Usually a copy of the dataset | Only a portion of the dataset |
| Main challenge | Keeping replicas up to date | Choosing a good shard key and routing requests |

## 5. Can we use both?

Yes. A large system can shard its data and also keep replicas for each shard. Sharding spreads the data, while replication keeps copies of each part.

## 6. When should we use them?

- Start with one database while it meets the application's needs.
- Consider **replication** when read traffic is high or additional copies are needed.
- Consider **sharding** when one database can no longer handle the data size or workload.
- Both add complexity, so use them when there is a clear need.

## 7. Quick revision

- **Replication = copy data.**
- **Sharding = split data.**
- Replication often helps scale reads and provides extra copies.
- Sharding distributes data and workload across servers.
- A system can use both.

## Related Notes

### Still not understanding Watch this: [ Replication vs Sharding Explained — Why Instagram Uses Both and Most Engineers Get It Wrong ](https://youtube.com/shorts/jRC5wE5hymo?si=XLccb-uUmFNazQf9)


- [Databases](../01-fundamentals/databases.md)
- [Scalability](./scalability.md)
- [Reliability](./reliability.md)
