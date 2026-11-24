# Indexing

---

## The Problem

Once a system grows and a large amount of data is stored in a database, data retrieval becomes slower — because we have to scan all the data for a single query.

```
Without Indexing:
Query: Find "John"

[Record 1] → scan ❌
[Record 2] → scan ❌
[Record 3] → scan ❌
[Record 4] → scan ❌  ...scans ALL records → slow 🐢
[Record N] → found ✅
```

---

## The Solution — Indexing

A separate, sorted data structure is used to locate rows in a database without scanning each and every record.

```
With Indexing:
Query: Find "John"

Index → points directly to → [Record 4] ✅  → fast 🚀
```

> **Analogy:** Imagine a library as a database and subjects as the index. Every subject's books are kept on their respective shelf — when requested, a book can be found without scanning the whole library.

---

## Types of Indexing

| Type | Description |
|---|---|
| **Clustered Indexing** | Rows in the database are physically sorted — the data itself is the index |
| **Non-Clustered Indexing** | Indexing is done on non-sorted rows; a separate structure points to the actual data |

```
Clustered:                          Non-Clustered:
┌──────────────────┐              ┌──────────┐     ┌──────────────────┐
│ ID │ Name  sorted│              │ Index    │ --> │ ID │ Name        │
├────┼─────────────┤              ├──────────┤     ├────┼─────────────┤
│ 1  │ Alice       │              │ Alice → 1│     │ 3  │ Bob         │
│ 2  │ Bob         │              │ Bob   → 2│     │ 1  │ Alice       │
│ 3  │ Charlie     │              │ Charlie→3│     │ 2  │ Charlie     │
└────┴─────────────┘              └──────────┘     └────┴─────────────┘
```

---

## Indexing Can Be Done On

| Index Type | Description |
|---|---|
| **Primary Key** | Indexing done on the primary key of a table |
| **Secondary Key** | Indexing done on a secondary key of the database |
| **Composite Key** | Indexing done on a combination of two or more columns |

---

## Why Not a Sorted Array?

Indexing is never done using a sorted array because insertion and deletion take **O(n)** time, which is not efficient for a database.

Instead, **MySQL** and **PostgreSQL** use **B-Tree** and **B+ Tree** for indexing.

---

## B-Tree and B+ Tree Data Structure

These data structures are used in modern databases for indexing because of their properties:

1. They follow the principles of a Binary Search Tree — nodes to the left of a parent are less than the parent, and nodes to the right are greater.
2. A node can have multiple keys/values.
3. All leaf nodes are at the same level.

```
            [10 | 20]
           /    |    \
       [5|7]  [12|15]  [25|30]
       / | \   / | \    / | \
      3  6  8 11 13 18 22 27 35    ← All leaf nodes at same level
```

> These properties give Search, Insertion, and Deletion a time complexity of **O(log n)**.

---

## Cassandra and LSM Trees

**Cassandra** is a database designed for write-heavy applications. It uses a technique called **Log-Structured Merge (LSM) Trees**.

It has three main components:

1. **Memtable** (Memory Level)
2. **SSTables** (On-Disk Storage)
3. **Compaction Process**

### How It Works

```
Write Request
     │
     ▼
┌─────────────┐    (when full)   ┌─────────────┐
│  Memtable   │ ─────────────>   │  SSTable 1  │
│  (In-Memory)│                  ├─────────────┤
└─────────────┘                  │  SSTable 2  │
                                 ├─────────────┤
                    Compaction   │  SSTable 3  │
                    merges ──>   └─────────────┘
                    SSTables
```

When data is inserted into Cassandra, it is first written to the **Memtable** — an in-memory data structure. When the Memtable reaches a certain size, it is flushed to disk as an immutable **SSTable**. Cassandra periodically performs **compaction**, during which multiple SSTables are merged to optimize storage and remove obsolete or deleted records.

### How Data Retrieval Works

```
Read Request
     │
     ▼
┌──────────────┐  found? ✅ → return latest version
│  Memtable    │
└──────┬───────┘
       │ not found ❌
       ▼
┌──────────────┐  found? ✅ → check timestamp → return latest
│   SSTables   │
└──────┬───────┘
       │ not found ❌
       ▼
  Return: No Result
```

Cassandra uses mechanisms such as **Bloom filters**, **partition indexes**, and **caches** to minimize unnecessary disk reads and improve read performance.

---

## Advantages and Disadvantages of Indexing

| | |
|---|---|
| ✅ **Advantage** | Faster read operations |
| ❌ **Disadvantage** | Slower write operations (index must be updated on every write) |

---

## When and Where to Index

1. **Data should be large** — indexing on 100 rows of data is not necessary.
2. **Index on the most queried columns** — know which columns receive the most queries.

> **Example:** During login, we query by email and phone number — so indexing should be done on the `email` and `phone_number` columns.

```
Most Queried Columns → Index These

┌──────────────┬────────────┬──────────────┐
│ email (IDX)  │ phone(IDX) │   password   │
├──────────────┼────────────┼──────────────┤
│ a@gmail.com  │ 9876543210 │  ********    │
│ b@gmail.com  │ 9123456789 │  ********    │
└──────────────┴────────────┴──────────────┘
```
