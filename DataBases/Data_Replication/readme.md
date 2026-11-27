# Database Replication

In a growing application, we need to make sure that a large amount of data is handled correctly and efficiently. We can vertically scale the database up to a certain limit, but at some point we will have to resort to horizontal scaling.

Once we have multiple databases aligned in parallel, we need to think about managing data across all of them.

This can be done using the following methods:

```
           ┌───────────────────────────────────────┐
           │         Data Management               │
           └────────────────┬──────────────────────┘
                            │
              ┌─────────────┴──────────────┐
              │                            │
       ┌──────▼──────┐              ┌──────▼──────┐
       │ Replication │              │  Sharding   │
       │  (copies)   │              │  (chunks)   │
       └─────────────┘              └─────────────┘
```

- **Replication** — The process of maintaining copies of data across multiple databases to provide high availability.
- **Sharding** — Data is split into multiple chunks and each chunk is stored in a separate database.

---

## The Problem

When a user wants to write to a database, the server needs to make sure the data is written to all replicas — which is a time-consuming process.

```
   User ──> Write Request ──> Server ──> DB 1 ✅
                                    ──> DB 2 ✅  (all must sync)
                                    ──> DB 3 ✅
```

---

## Solution — Replication Algorithms

### 1) Leader-Follower Architecture (Master-Slave)

In this type of implementation, we split the databases into 2 roles:
- **Master (Leader):** A single database named the master — the one in charge.
- **Slave (Follower):** All remaining databases are slaves that follow the master's instructions.

```
              Write                    Read
   User ──────────────> [ Master ] <──────────── User
                             │
              ┌──────────────┼──────────────┐
              │              │              │
         [ Slave 1 ]    [ Slave 2 ]    [ Slave 3 ]
              ▲              ▲              ▲
              └──────── updates sent ───────┘
```

Every **write** query goes through the master, and every **read** query goes through a slave.
Once a write query is successful on the master, it sends updates to all slaves.

The Master-Slave architecture follows 2 methods to manage write transactions:

#### a) Synchronous Replication

The master ensures that data is written to all slaves first, and then marks the transaction as complete.

```
   Master ──> write ──> Slave 1 ✅
                    ──> Slave 2 ✅
                    ──> Slave 3 ✅
                    ──> Transaction Marked Complete ✅
```

| | |
|---|---|
| ✅ **Advantage** | Strong consistency |
| ❌ **Disadvantage** | Slow |

> Usually used in applications where consistency matters — e.g., **Banking Systems**.

---

#### b) Asynchronous Replication

When the master receives a transaction request, it completes the transaction first and then sends update requests to the slaves.

```
   Master ──> write ──> Transaction Marked Complete ✅
                    ──> (later) Slave 1 updated
                    ──> (later) Slave 2 updated
                    ──> (later) Slave 3 updated
```

| | |
|---|---|
| ✅ **Advantage** | Fast and highly available |
| ❌ **Disadvantage** | Consistency is not guaranteed — slaves may have stale data for some time |

> Used in applications where high availability takes priority over consistency — e.g., **Social Media Applications**.

---

#### c) Semi-Synchronous Replication

Since both of the above have slight disadvantages, most systems use a middle ground:

The master receives a write request, updates **one slave**, marks the transaction as complete, and then updates all remaining slaves later.

```
   Master ──> write ──> Slave 1 ✅ ──> Transaction Complete ✅
                    ──> (later) Slave 2 updated
                    ──> (later) Slave 3 updated
```

---

### 2) Multi-Leader-Slave Architecture

In this replication technique, there are multiple masters, each with their own slaves. When a write request reaches a master, it updates all corresponding masters, and then each master sends updates to its own slaves.

```
        Write                           Write
   User ──────> [ Master 1 ] <────> [ Master 2 ] <────── User
                     │                    │
         ┌───────────┤          ┌─────────┤
    [Slave 1] [Slave 2]    [Slave 3] [Slave 4]
```

> If two masters receive a write request at the same time with different data, the data with the **latest timestamp** is kept.

---

### 3) Leaderless Architecture

In this technique, there is no designated master. Instead, write requests are sent to multiple nodes simultaneously, and the transaction is considered successful once a **majority of nodes** confirm it. On read, the data with the latest timestamp is returned.

```
   User ──> Write ──> Node 1 ✅
                  ──> Node 2 ✅  (majority reached → success)
                  ──> Node 3 ⏳

   User ──> Read  ──> Node 1
                  ──> Node 2  → return latest timestamp ✅
                  ──> Node 3
```
