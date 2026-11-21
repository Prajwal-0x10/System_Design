# Measuring and Estimating a System

System Design is nothing but designing a scalable system.

## Problems While Designing a System

1. **Over Spending** — This issue is caused when we try to meet requirements without caring about cost.
    - e.g. Scaling a system vertically is easy but increases the cost exponentially.

2. **Under Engineering** — It is caused when we do not estimate or measure the requirements of a system and its expectations.

---

## Criteria to Design a System

### 1) Latency
It is the time taken to communicate between any two services.

> **e.g.** A client wants to send a request to a server — the time taken for it to reach the server is known as latency.

| Latency | System Rating |
|---|---|
| Less than 100ms | ✅ Very Good System |
| Between 100ms and 300ms | 🟡 Good System |
| Between 300ms and 1 sec | 🟠 Noticeable delay, but works |
| Greater than 1 sec | ❌ Bad System |

---

> **Before going to Throughput and Bandwidth, we should assume:**
> 1. Normal traffic is the Throughput.
> 2. Peak traffic will be **3× of Throughput**.
>
> Always design your system according to **Peak Traffic**.

---

### 2) Throughput
Total number of requests a system can handle in 1 second is known as Throughput.

> **e.g.** If a system can handle 1000 requests per second, the Throughput of the system will be **1000 Req/Sec**.

### 3) Bandwidth
The amount of data a system can handle in 1 second is known as Bandwidth, usually measured in bits/sec.

> **e.g.** If a system can handle 1000 requests and each request is of 1 KB, the bandwidth required for the system will be:
>
> `Bandwidth = 1000 req/s × 1 KB/req × 1024 Bytes/KB × 8 bits/Byte = 8,192,000 bits/s ≈ 8 Mbps`

### 4) Read Heavy and Write Heavy
Any system can be defined in 3 categories — **Read Heavy**, **Write Heavy**, or **Balanced**.

- **Read** — Reading data from the system.
- **Write** — Writing (uploading) data into the system.

| Application | Type |
|---|---|
| YouTube | Read Heavy |
| Uber | Write Heavy |
| Google Drive | Write Heavy |
| WhatsApp | Balanced |

### 5) Percentiles
Percentiles are a way of measuring the performance of a system where, rather than using the average, we use percentiles to get feedback on how our system is working.

- **P95** — Measures how our system is performing for 95% of users.
- **P99** — Measures how our system is performing for 99% of users.

**Example:** We have a total of 100 users. 90 users get 50ms latency and 10 users get 2000ms latency.

```
Average Latency = (90 × 50 + 10 × 2000) / 100 = 245 ms
```

It seems our system falls in the **Good** category, but 10% of users are unhappy with our system and might not use it anymore.

Hence, P95 and P99 are calculated:
- **P95** — How the 95th user is experiencing the system → **2000 ms** in this case.
- **P99** — How the 99th user is experiencing the system → **2000 ms** in this case.

Since the answer is 2000ms, we will focus on improving our system's latency.

---

## Practical Calculation — Designing a Small System

**Requirements:** Design an Instagram-like application where:
1. 100 Cr Daily Users
2. 1 user sends 50 messages per day (Write Operations)
3. A message can be simple text or a photo upload
4. A user sends 90% text messages with an average size of 1 KB
5. A user sends 10% photos with an average size of 5 MB

**Problem Statement:**
1. Find Throughput
2. Find Bandwidth
3. If a server can handle 10⁴ requests/sec, how many servers are required?

---

### 1) Throughput (Number of requests per second)

```
Total Users            = 100 Cr = 10⁹ users
Total requests per day per user  = 50
Total requests per sec per user  = 50 / (24 × 60 × 60)

Total requests for 10⁹ users = (50 × 10⁹) / (24 × 60 × 60) = 578,703 req/sec
```

### 2) Bandwidth (Data flow per second)

```
Total Users = 10⁹
Requests per user per day = 50 (45 text + 5 photos)

Text message per user per sec =
    (45 × 1KB × 1024 Bytes/KB × 8 bits/Byte) / (24 × 60 × 60)

Photo message per user per sec =
    (5 × 5MB × 1024 KB/MB × 1024 Bytes/KB × 8 bits/Byte) / (24 × 60 × 60)

Total Bandwidth for all users =
    10⁹ × ((45 × 1KB × 1024 Bytes/KB × 8 bits/Byte) + (5 × 5MB × 1024 KB/MB × 1024 Bytes/KB × 8 bits/Byte)) / (24 × 60 × 60)

= 2,432 × 10⁹ bits/s
= 2,432,000,000,000 bits/s
= 2,432 Gbps
≈ 2.43 Tbps
```

### 3) Number of Servers Required

```
Throughput      = 578,703 req/sec
Peak Throughput = 3 × 578,703 = 1,736,109 req/sec
Server Capacity = 10⁴ req/sec

Servers Required = Peak Throughput / Server Capacity
                 = 1,736,109 / 10⁴
                 ≈ 174 Servers
```
