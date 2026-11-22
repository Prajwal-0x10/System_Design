# Scaling

---

## Vertical Scaling

It is a type of scaling where the system is scaled up by upgrading the hardware components of the system.

**Example:** Increasing RAM, increasing memory storage, increasing CPU cores.

```
         Before                        After
    ┌──────────────┐             ┌──────────────────┐
    │   Server     │   ──────>   │   Server ⬆       │
    │   4GB RAM    │             │   16GB RAM       │
    │   256GB SSD  │             │   1TB SSD        │
    └──────────────┘             └──────────────────┘
```

| | |
|---|---|
| ✅ **Advantages** | No code change required |
| ❌ **Disadvantages** | Limited scalability, exponentially increasing cost, single point of failure, downtime while upgrading |

---

## Horizontal Scaling

To overcome the disadvantages of vertical scaling, we use Horizontal Scaling. In Horizontal Scaling, we add multiple servers with the same or different configuration to handle the load.

**Example:** A server with 8 GB RAM and 512 GB storage is added in parallel to the system.

```
         Before                        After
    ┌──────────────┐             ┌──────────────┐
    │   Server 1   │   ──────>   │   Server 1   │
    └──────────────┘             ├──────────────┤
                                 │   Server 2   │
                                 ├──────────────┤
                                 │   Server 3   │
                                 └──────────────┘
```

| | |
|---|---|
| ✅ **Advantages** | High scalability, no downtime while upgrading, linearly increasing cost, fault tolerant |
| ❌ **Disadvantages** | Complex to handle, code change required |

> **Note:** Always scale the system vertically first until it reaches its threshold, and then scale it horizontally.

---

## Statelessness vs Statefulness

**Problem:** When a user sends a request and the load balancer processes it via Server 1, then the user sends another request and the load balancer routes it to Server 2 — Server 2 will not have the context of the first request. This is known as **Statefulness**, where servers are dependent on context stored on the server itself.

```
   User ──> Request 1 ──> Load Balancer ──> Server 1 (has context)
   User ──> Request 2 ──> Load Balancer ──> Server 2 (❌ no context!)
```

**Solution:** **Cookies** — Cookies are nothing but the context of a user session. These cookies are stored in a shared location where all parallel servers can access the context and return a response to the user.

```
   User ──> Request 1 ──> Load Balancer ──> Server 1 ──> Shared Store (cookie saved)
   User ──> Request 2 ──> Load Balancer ──> Server 2 ──> Shared Store (cookie read ✅)
```

This property of a system — where it is not dependent on a specific server for user context — is known as **Statelessness**.

---

## Load Balancer

```
                               ┌─────────────┐
                          ┌──> │   Server 1  │
   User ──> Load Balancer ├──> │   Server 2  │
                          └──> │   Server 3  │
                               └─────────────┘
```

| Feature | Description |
|---|---|
| **Traffic Control** | The main job of a load balancer is to control the traffic of the system and send it to the appropriate servers |
| **Health Check** | It periodically checks if the servers are functioning properly or not |
| **Scalability** | It can add or remove servers — if a server is not functioning properly, it removes it from the pool and stops sending requests to it |

---

## Types of Load Balancing Techniques

### 1) Round Robin

The load balancer distributes tasks based on the server's numerical order.

```
   t1 ──> Server 1
   t2 ──> Server 2
   t3 ──> Server 3
   t4 ──> Server 1  (cycle repeats)
   t5 ──> Server 2
```

**Example:** t1→s1, t2→s2, t3→s3, t4→s1 and so on.

| ❌ **Disadvantage** | If there is an ongoing heavy task, the load balancer will still try to assign a new task to the same server, which can choke it |
|---|---|

---

### 2) Load Based

To overcome the disadvantage of Round Robin, we use Load Based distribution, where each server is assessed based on how much capacity it has and tasks are assigned accordingly.

```
   Server 1 ── 70% capacity used  ──┐
   Server 2 ── 80% capacity used  ──┤──> Load Balancer assigns to Server 3
   Server 3 ── 50% capacity used  ──┘    (least loaded)
```

**Example:**
- Server 1 — 70% equipped
- Server 2 — 80% equipped
- Server 3 — 50% equipped → new request is assigned to Server 3

---

### 3) Weighted Round Robin

Each server is assigned a weight according to its capacity, and tasks are distributed proportionally.

```
   Server 1 ── 8GB  RAM ── Weight: 1  ──> gets 1 out of every 7 requests
   Server 2 ── 16GB RAM ── Weight: 2  ──> gets 2 out of every 7 requests
   Server 3 ── 32GB RAM ── Weight: 4  ──> gets 4 out of every 7 requests
```

Out of every 7 requests: 1 goes to Server 1, 2 go to Server 2, and 4 go to Server 3.

---

### 4) Hash Based

Tasks are divided among servers based on the type of action they perform.

```
   Login / Logout  ──> Server 1 (always)
   File Upload     ──> Server 2 (always)
   Search Query    ──> Server 3 (always)
```

**Example:**
- Login and logout are always handled by Server 1.
- Uploading is always handled by Server 2.
- And so on.

---
