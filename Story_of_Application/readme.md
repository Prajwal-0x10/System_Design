# Story of a Growing System

Every system follows the following stages of evolution —
from a simple single-server application to a complex distributed system.

---

## Stage 0 — Initial Planning

A system is set up with one server where both the code and the database reside on the same server.

```
                ┌────────────┐
                │   Code     │
   User  <----> │            │
                │  Database  │
                └────────────┘
```

---

> **Before moving to Stage 1, here are some important terms to know:**
>
> 1. **Bottleneck** — A point where the flow of data or traffic is restricted, causing a delay in system response.
> 2. **Single Point of Failure** — A point or component of a system which, when it fails, collapses the whole system.
> 3. **Redundancy** — A characteristic of a system that ensures a duplicate component is available in case the original single point of failure component fails.

---

## Stage 1 — Separating Code and Database

**Problem:** When the system grows, it receives more requests. At some point, the requests may choke the system operations, causing resource stealing — the code process may require more CPU, stealing it from the database.

**Solution:** Ensure there are separate resources for both the database and the code to guarantee proper distribution of resources.

- **DB** — May require more storage but can work on less CPU.
- **Code** — May require more CPU but can work on less storage.

```
                ┌────────────┐        ┌────────────┐
                │            │        │            │
   User  <----> │    Code    │ <----> │  Database  │
                │   Server   │        │   Server   │
                └────────────┘        └────────────┘
```

---

## Stage 2 — Vertical Scaling

**Problem:** As the system grows and the number of requests grows significantly, the single code server is no longer able to handle them and may choke the system.

**Solution:** To tackle this problem, **Vertical Scaling** is used. Vertical Scaling is the process of upgrading the hardware of the current system — such as RAM, Storage, CPU, etc.

```
                ┌─────────────────┐        ┌────────────┐
                │  Code Server    │        │            │
   User  <----> │  (Upgraded ⬆️)  │ <----> │  Database  │
                │  More CPU/RAM   │        │   Server   │
                └─────────────────┘        └────────────┘
```

| | |
|---|---|
| ✅ **Advantage** | Simple and no code change required |
| ❌ **Disadvantage** | Limited scalability, downtime, exponentially increasing cost, single point of failure |

---

## Stage 3 — Horizontal Scaling

**Problem:** Avoid the disadvantages caused by vertical scaling.

**Solution:** **Horizontal Scaling** is used to overcome this problem. It is the process of adding new systems in parallel, with the same hardware configuration as the existing one.

```
                             ┌────────────┐
                        ┌──> │  Server 1  │ ─┐
                        │    └────────────┘  │
   User  <----------->  │    ┌────────────┐  │ <----> │  Database  │
                        ├──> │  Server 2  │ ─┤
                        │    └────────────┘  │
                        │    ┌────────────┐  │
                        └──> │  Server 3  │ ─┘
                             └────────────┘
```

| | |
|---|---|
| ✅ **Advantage** | Linearly increasing cost, eliminates single point of failure, high scalability |
| ❌ **Disadvantage** | Increases complexity of the system |

---

## Stage 4 — Load Balancer

**Problem:** How will the system know which server will handle a given request?

**Solution:** A **Load Balancer** is used — a component that handles incoming traffic and distributes it among the servers.

> A load balancer can itself be a single point of failure, so we use the **redundancy** property of the system — establishing another load balancer as a backup that takes over if the primary fails.

```
                        ┌─────────────────┐     ┌────────────┐
                        │  Load Balancer  │ ──> │  Server 1  │ ─┐
   User  <----------->  │   (Primary)     │ ──> │  Server 2  │  │ <----> │ Database │
                        │   (Backup  🔁)  │ ──> │  Server 3  │ ─┘
                        └─────────────────┘     └────────────┘
```

---

## Stage 5 — Caching

**Problem:** When read requests grow and the database is not able to handle them, the database gets choked.

**Solution:** **Caching** is used. Caching is the process of storing frequently accessed data in memory (RAM) so that it can be retrieved quickly, which reduces the load on the database.

```
                        ┌──────────────┐    ┌───────────┐
                        │ Load Balancer│    │   Cache   │
   User  <----------->  │              │ -> │   (RAM)   │
                        │              │    └─────┬─────┘
                        └──────────────┘          │ (miss)
                                                  ▼
                                           ┌────────────┐
                                           │  Database  │
                                           └────────────┘
```

---

## Stage 6 — Database Replication (Primary & Secondary)

**Problem:** When write requests grow and the database is not able to handle them, the database gets choked again.

**Solution:** Split the database into a **Primary** and **Secondary** database:
- All **write** operations are handled by the **Primary DB**.
- All **read** operations are handled by the **Secondary DB**.

The Primary DB frequently transfers data to the Secondary DB as part of **replication**.

```
                        ┌──────────────┐    ┌────────────────┐
                        │ Load Balancer│    │  Primary DB    │ (Writes)
   User  <----------->  │              │ -> │                │
                        │              │    └───────┬────────┘
                        └──────────────┘            │ replication
                                                    ▼
                                           ┌────────────────┐
                                           │  Secondary DB  │ (Reads)
                                           └────────────────┘
```

---

## Stage 7 — Object Storage

**Problem:** There will be a point when it becomes hard to manage data since it has grown too large.

**Solution:** Data is stored as **objects** locally, where every object represents the location of the data on a storage service. **Amazon S3 (Simple Storage Service)** is a popular storage service used for this purpose.

```
                        ┌──────────────┐    ┌────────────┐
                        │ Load Balancer│    │  Servers   │
   User  <----------->  │              │ -> │            │
                        └──────────────┘    └─────┬──────┘
                                                  │
                                     ┌────────────┴────────────┐
                                     │                         │
                               ┌─────▼──────┐       ┌─────────▼──────┐
                               │  Database  │       │  Object Store  │
                               │            │       │   (e.g. S3)    │
                               └────────────┘       └────────────────┘
```

---

## Stage 8 — Message Queue

**Problem:** When a task is already being performed by the system and a new task arrives, the new task has to wait until the current task is completed before it can be executed.

**Solution:** A **Queue** is used to manage all tasks. High-priority tasks are executed immediately, while lower-priority tasks are added to the queue to be executed later.

```
                        ┌──────────────┐    ┌─────────────┐    ┌──────────────┐
                        │ Load Balancer│    │   Servers   │    │   Message    │
   User  <----------->  │              │ -> │             │ -> │    Queue     │
                        └──────────────┘    └─────────────┘    └──────┬───────┘
                                                                       │
                                                               ┌───────▼───────┐
                                                               │    Workers    │
                                                               └───────────────┘
```

---

## Stage 9 — Sharding

**Problem:** How to manage huge amounts of data?

**Solution:** **Sharding** is used to manage large volumes of data. It is the process of splitting data into multiple chunks and storing them across multiple databases.

```
                        ┌──────────────┐    ┌─────────────┐
                        │ Load Balancer│    │   Servers   │
   User  <----------->  │              │ -> │             │
                        └──────────────┘    └──────┬──────┘
                                                   │
                             ┌─────────────────────┼─────────────────────┐
                             │                     │                     │
                      ┌──────▼──────┐       ┌──────▼──────┐      ┌──────▼──────┐
                      │   DB Shard  │       │   DB Shard  │      │   DB Shard  │
                      │     #1      │       │     #2      │      │     #3      │
                      └─────────────┘       └─────────────┘      └─────────────┘
```

---

## Stage 10 — Microservices Architecture

Finally, to manage the growing application, each individual functionality of the system is split into separate services that are **loosely coupled**. This is popularly known as **Microservices Architecture**.

```
                        ┌──────────────┐
                        │ Load Balancer│
   User  <----------->  │  / API Gate  │
                        └──────┬───────┘
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
   ┌──────▼──────┐      ┌──────▼──────┐     ┌──────▼──────┐
   │   Service   │      │   Service   │     │   Service   │
   │   (Auth)    │      │  (Messaging)│     │   (Media)   │
   └──────┬──────┘      └──────┬──────┘     └──────┬──────┘
          │                    │                    │
   ┌──────▼──────┐      ┌──────▼──────┐     ┌──────▼──────┐
   │    DB / Cache        DB / Queue        Object Store  │
   └─────────────┘      └─────────────┘     └─────────────┘
```

> Each service has its own database, scales independently, and is loosely coupled from the rest of the system.
