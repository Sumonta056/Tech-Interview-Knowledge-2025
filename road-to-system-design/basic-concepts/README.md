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
