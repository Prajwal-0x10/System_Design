# How the Internet Works

The Internet works on a **Client-Server Model**.
- A **Client** is a component that generates a request, whereas a **Server** serves the request with a response.
- Clients and Servers are connected using routers and switches in the form of networks.

---

## Basic Terminologies

### 1) IP Address
An IP Address is the address of a device present on the internet, which helps in the navigation of data packets.

There are two types of IP Addresses:

- **a) IPv4** — It is a 32-bit address made of four 8-bit octets.
  - Example: `192.134.162.122`

- **b) IPv6** — It is a 128-bit hexadecimal address.
  - Example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

### 2) DNS
Referred to as **Domain Name System**, it represents a human-readable domain name mapped from a machine-readable IP Address.

### 3) DNS Resolver
It is a component that returns the nearest server's IP address to the client in exchange for a Domain Name.

### 4) Packet
It is a unit of data sent over the internet, which contains all details from source to destination — including the IP address of the source and destination, and a sequence number to arrange data at the destination.

### 5) Routing
The process of finding the best path for data packets to travel from source to destination.

**Steps to route to destination:**
1. Find the device with the longest matching IP prefix.
2. Hop to that device.
3. Keep doing this until the destination is reached.
4. If the hop count becomes zero, the packet is discarded, preventing an infinite loop.
