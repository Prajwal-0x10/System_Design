# Database Concepts

There are 2 major types of databases:

---

## 1) SQL Based Databases (Relational Database)

- **SQL** stands for **Structured Query Language**.
- These are databases where data is stored in a table format in rows and columns.
- Each row represents a record and each column represents a feature of that record.
- **MySQL** and **PostgreSQL** are some of the best known SQL-based databases.

**Example:** Customer records stored in a database where each row represents a customer and each column represents their details.

```
┌──────────────┬─────────────────┬──────────────┐
│     Name     │     Address     │ Phone Number │
├──────────────┼─────────────────┼──────────────┤
│  John Doe    │  123 Main St    │  9876543210  │
│  Jane Smith  │  456 Elm Ave    │  9123456789  │
└──────────────┴─────────────────┴──────────────┘
```

### ACID Properties

SQL databases follow **ACID** properties:

| Property | Description | Example |
|---|---|---|
| **Atomicity** | Ensures that an action is either fully completed or not at all — no partial operations | When sending money, it makes sure the amount is both debited and credited. If it fails, the transaction is rolled back |
| **Consistency** | Ensures data is always in a valid state and follows all rules set by the database | A bank transaction will fail if the balance would go negative — maintaining valid state |
| **Isolation** | Ensures that even if an action is performed multiple times, the result remains the same | Pressing the send button multiple times in a single transaction will only send money once |
| **Durability** | Ensures that data is not lost in any failure case | If the bank server fails mid-transaction, the data will not be lost |

### Joins
Joins are operations that merge multiple relational databases to perform a required action.

```
┌──────────────┐        ┌──────────────────┐
│  Customers   │  JOIN  │     Orders       │
│─────────────-│ ─────> │──────────────────│
│ CustomerID   │        │ OrderID          │
│ Name         │        │ CustomerID (FK)  │
│ Address      │        │ Amount           │
└──────────────┘        └──────────────────┘
```

| | |
|---|---|
| ✅ **Advantages** | Joins make it easy to retrieve complex data from multiple relational databases; follows ACID properties which ensures data safety |
| ❌ **Disadvantages** | Hard to scale |

---

## 2) NoSQL Databases (Non-Relational Database)

- These are unstructured databases where data is **not** stored in table format.
- Data is stored as **key-value pairs**, where each value is assigned to a unique key.
- The value can be anything — from simple text to photos or documents.

```
┌─────────────────┬────────────────────────────────────┐
│       Key       │               Value                │
├─────────────────┼────────────────────────────────────┤
│  Customer_001   │  { Name, Address, Phone, ... }     │
│  Customer_002   │  { Name, Address, Phone, ... }     │
└─────────────────┴────────────────────────────────────┘
```

### Well-Known NoSQL Databases

#### 1) MongoDB (Document Based)
Data is stored in key-value format where the value is a document of relevant data. All information is retrieved by reading the key.

```
Customer_ID : {
    Name        : "John Doe",
    Address     : "123 Main St",
    Phone_Number: "9876543210"
}
```

#### 2) Cassandra (Wide Column)
Best known for storing data in wide columns while maintaining relations. Used when high volumes of write operations are required.

```
┌─────────────────┬────────────────────────┬──────────┐
│   User1-User2   │  User1-User2-User3     │  User3   │
├─────────────────┼────────────────────────┼──────────┤
│  msg1, msg2...  │  msg1, msg2...         │  msg1... │
└─────────────────┴────────────────────────┴──────────┘
```
> **Example Use Case:** Chat history in a messaging application.

#### 3) Neo4j (Graph Based)
Uses a graph structure to link connections and relationships between nodes.

```
   [Alice] ──friends──> [Bob] ──friends──> [Charlie]
      │                                        │
      └──────────── recommends ───────────────>│
```
> **Example Use Case:** Social connections, recommendation engines.

### BASE Properties

NoSQL databases follow **BASE** properties:

| Property                  | Description                      |
|---------------------------|----------------------------------|
| **Basically Available**   | The database is always available |
| **Soft State**            | May be out of sync for some time |
| **Eventually Consistent** | Becomes consistent over time     |

| | |
|---|---|
| ✅ **Advantages** | Easy to scale |
| ❌ **Disadvantages** | Hard to run complex queries |

---

## Choosing the Right Database

As an engineer you should:
- Have a clear understanding of the use case before choosing a database.
- A growing application may use multiple types of databases for different functionalities.

**Example:** Considering a food delivery application —

```
┌─────────────────────┬──────────────────────────────────┐
│    Functionality    │         Database Choice          │
├─────────────────────┼──────────────────────────────────┤
│  Menu / Catalog     │  MongoDB (Document Based)        │
│  Payments           │  SQL / PostgreSQL (ACID needed)  │
│  Recommendations    │  Neo4j (Graph Based)             │
└─────────────────────┴──────────────────────────────────┘
```
