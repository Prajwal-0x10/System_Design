# Introduction to High Level Design

## What is System Design?

A system is a set of components that work together to serve a purpose, and System Design is a process of structuring a software/hardware system to serve a specific purpose.

As a developer, we should always use a requirement-first approach to design a system.

## Principles of System Design

Every system follows the below design principles:

### 1) Scalability / Load
- **Load** is the amount of work a system has to do.
- **Scalability** is a measure of how flexible a system is to handle this load.

A system can be horizontally or vertically scalable.

> **Example:** If some new users are added and the system is able to handle the load, then the system is scalable.

### 2) Performance
- **Latency** is defined as the delay between a request to a system and the response by that system.
- It is a measure of how well or how fast a system responds to a given request.

### 3) Availability
- It is defined as how much a system is available to serve the requests.
- Measured in units of 9s.

> **Example:** System A is 99% available, System B is 99.9% available.

### 4) Reliability
- Can be defined as how trustworthy a system is and how well it follows the instructions given.

### 5) Consistency
- Consistency is a measure of how a system ensures that it does not violate any constraints.

> **Example:** Actual balance in a bank is ₹500 but shown as ₹400.

### 6) Durability
- Durability is a measure of how well a system ensures that it does not lose any data.

> **Example:** Bank balance is ₹500 before the server went down but shown as ₹400 after.

### 7) Cost
- How cost-effective a system is. We cannot always use the most expensive or best available technology for our system.

### 8) Maintainability
- How easy it is to manage and maintain the system.

### 9) Security
- How secure the system is from external threats.

---

> There can always be a **trade-off** between these system design principles based on business requirements.

**Examples:**
- A bank can trade off availability but cannot trade off consistency and reliability.
- A website can trade off consistency but cannot trade off availability.
