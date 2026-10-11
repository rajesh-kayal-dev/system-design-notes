# Databases

## 1. What Is a Database?

A **database** is an organized place to store data so an application can find, add, update, or delete it when needed.

Example: A task management app stores each task's title, description, status, and owner in a database.

## 2. Why Do We Need a Database?

A database helps an application:

- Keep data after the app is closed.
- Find information when it is needed.
- Add, update, and delete records.
- Manage data for many users.
- Control access and protect information.

## 3. How Does It Work?

1. The client sends a request to the backend.
2. The backend checks the request and decides what data is needed.
3. The backend asks the database to read or change the data.
4. The database returns a result.
5. The backend sends a response to the client.

In most web applications, the backend connects to the database. The client should not connect directly to a private database.

## 4. Main Types of Databases

There are many database types, but two common choices for web applications are **SQL** and **NoSQL**.

| Type | How data is stored | Common examples |
|---|---|---|
| **SQL (relational)** | In tables with rows and columns, usually with a defined structure | PostgreSQL, MySQL |
| **NoSQL (document)** | In flexible documents, often similar to JSON | MongoDB |

### Simple example

A SQL task table might look like this:

| id | title | status |
|---|---|---|
| 1 | Learn networking | todo |
| 2 | Practice API design | completed |

A MongoDB document for a task might look like this:

```json
{
  "title": "Learn networking",
  "status": "todo"
}
```

Neither type is always better. Choose based on the data, relationships, queries, and needs of the application.

## 5. Important Database Terms

| Term | Simple meaning |
|---|---|
| **Table** | Data arranged in rows and columns in a relational database |
| **Row / Record** | One item stored in a table |
| **Document** | One data item in a document database |
| **Schema** | The structure or rules for how data is organized |
| **Query** | A request to read or change data |
| **Primary key** | A value that uniquely identifies a row |
| **Index** | A data structure that can help the database find information faster |
| **Transaction** | A group of database operations handled as one unit |

## 6. What Should We Consider When Choosing a Database?

- **Data structure:** Does the data have clear relationships, or does it vary between records?
- **Queries:** How will the application search, filter, and update the data?
- **Consistency:** Does the application need changes to be visible together and correctly?
- **Scale:** How much data and how many requests are expected?
- **Operations:** Can the team run, secure, back up, and maintain the database?

Start with a database that fits the current requirements. Do not choose one only because it is popular.

## 7. Database Basics for System Design

As an application grows, a designer may also consider:

- **Indexing:** Helps speed up common searches, but uses storage and can slow down writes.
- **Replication:** Keeps copies of data on multiple database servers, often to improve availability or read capacity.
- **Sharding:** Splits data across multiple servers when one server is not enough.
- **Backups:** Copies of data used to recover after accidental deletion or failure.

You do not need all of these on day one. Add them when the system's requirements justify them.

## 8. Quick Revision

- A database stores and organizes application data.
- SQL databases use tables; document databases such as MongoDB store documents.
- The backend usually reads and writes database data.
- An index can make searches faster.
- Replication keeps copies of data; sharding splits data across servers.
- Choose a database based on requirements, not popularity.

**Remember:** First understand what data the application needs and how it will use that data. Then choose the database.

## Related Notes

- [System Design Basics](./system-design-basics.md)
- [Client-Server Architecture](./client-server.md)
- [API Design](./api-design.md)
- [Networking](./networking.md)
