---
icon: calculator-simple
---

# Basic Concepts

Learning Trade-Offs

***

* Horizontal Scaling vs Vertical Scaling
* Strong Consistency vs Eventual Consistency
* SQL vs NoSQL Database
* Read Replicas vs Sharding
* Monolithic vs Microservice Architecture
* Consistency with Partition Tolerance vs Availability with Partition Tolerance

***

**Server**: When your application is running, the server has an IP address and a port to access it. A server is normally a virtual machine, like an  AWS Virtual Machine (EC2 Instance)

**Latency:** The amount of time taken for a request to travel from client to server or server to client

**RTT (Round Trip Time):** Total amount of time taken for a request to travel Client -> Server -> Client

**Throughput:** The Number of requests the system can handle per second

**Scaling:** Need to increase the specs of the VM machine (RAM, CPU, Storage, and add more machines.

1. **Vertical Scaling:** increasing RAM, CPU, Storage, and is mainly used for SQL DB
2. **Horizontal Scaling:** adding more EC2 machines and distributing the load. Used more in real cases
3. **Autoscaling:** Use only the required number of instances at first, if more requests come instance automatically increases

**Load Balancer:** Distributes requests among multiple EC2 machines, handles the client request traffic

**Load Estimation:** Number of reads and writes.

* Twitter has 100M daily users, and one user posts 10 tweets a day
* Number of writes per day = 100M \* 10 = 1B
* 1 user reads almost 100 tweets a day
* Number of read per day = 100M \* 100 = 10B

**Storage Estimation**: amount of storage used per day

**Resource Estimation**: Total number of CPUs and servers required

* Server gets 10k requests per second, each request takes 10ms to process
* Total CPU Time to process: 10K \* 10ms = 100,000ms
* 1 core of CPU can handle 1000ms of processing per second
* Total Core Needed = 100,000/1000 = 100 core
* 1 Server has 4 cores
* Total Server Needed = 100/4 = 25 Server needed

**CAP Theorem:**

In a distributed system, only 2 of the 3 properties can happen together; it's impossible to achieve all

* **Consistency:** All nodes have the same data at a time, consistency between clusters. If there is an update in node B, then data replication happens, and B will propagate that update to A and C.
* **Availability:** Continue serving when a node failure happens with available healthy nodes. If the B node failed, the request will be served by node A and C
* **Partition Tolerance:** The System continues to operate even if there is a failed communication breakdown between different nodes. (As we already achieved Consistency and Availability, it's impossible for this to happen)
* Possible: CA, CP, AP - Impossible: CAP
* In practice, only CP or AP causes there will always be a partition happening
* For banking, Consistency matters most, and Social Media Availability



#### 🗄️ 1. Sharding

**Real-life analogy: A library with too many books**

Imagine a single librarian managing 10 million books. Finding any book takes forever. So what do you do?

You **split the books across 10 different rooms** — Room A has books starting with A-C, Room B has D-F, and so on. Now each librarian handles only their room. Much faster.

**That's sharding.**

In databases, when your data gets too big for one server, you split it across multiple servers. Each server holds a **"shard"** — a portion of the total data.

> User IDs 1–1M → Server 1 User IDs 1M–2M → Server 2 User IDs 2M–3M → Server 3

When a request comes in for User 1,500,000 — the system knows exactly which server to go to.

**Why it matters:** One database server has limits. Sharding lets you scale **horizontally** — just add more servers.

***

#### 🌍 2. TLS to Nearest Region (GeoDNS / Anycast Routing)

**Real-life analogy: McDonald's branches**

You're hungry. Would you drive to a McDonald's 50km away when there's one 2km from you? No.

When your app sends a request to a server, the same logic applies. If your server is in the US but your user is in Bangladesh — that's a long trip. Slow response.

**TLS to nearest region** means: route the user's request to the **closest server** geographically that can handle it securely (TLS = the secure HTTPS handshake).

> User in Dhaka → hits Singapore server User in London → hits Frankfurt server User in New York → hits Virginia server

**Why it matters:** Less distance = less latency = faster app. Companies like Cloudflare, AWS CloudFront do this automatically.

***

#### 📨 3. Topic Partition in Kafka

**Real-life analogy: A busy post office with multiple counters**

Imagine thousands of letters arriving at a post office every second. One counter can't handle it all. So you open **10 counters**, and letters are distributed across them.

In Kafka, a **Topic** is like the category of mail — say, "Order Updates."

A **Partition** is one counter handling a slice of that mail.

```
Topic: "order-updates"
├── Partition 0 → handles orders from users A-D
├── Partition 1 → handles orders from users E-J
└── Partition 2 → handles orders from users K-Z
```

Multiple **consumers** (workers) can read from different partitions **simultaneously** — so processing is parallel and fast.

**Why it matters:** Without partitions, one Kafka topic = one queue = bottleneck. Partitions = parallelism = scale.
