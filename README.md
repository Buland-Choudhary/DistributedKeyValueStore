# 🚀 Distributed Key‑Value Store

This project delivers a **scalable**, **robust**, and **consistent** key‑value storage system built in Java and deployed on AWS EC2. It evolved from a **single‑node** prototype with at‑most‑once semantics to a **multi‑node** distributed service with **consistent hashing**, **epidemic membership**, and **replication**.

---

## 📌 Project Phases & Milestones

1. **Single‑Node KV Store**
   - Implemented **put/get/remove** with Protobuf request/reply over UDP  
   - At‑most‑once semantics via a **caching** layer for duplicate‑request detection  
   - Custom error codes: success, not-found, out‑of‑space, overload, internal-failure  
   - Enforced **64 MB heap limit**, optimized for packet loss, duplication, and reordering  

2. **Consistent‑Hashing & Routing**
   - Scaled to **20+ nodes** using **consistent hashing** for key partitioning  
   - Static peer list for bootstrap; **shutdown/wipeout** commands for failure injection  
   - Implemented **epidemic membership** protocol to track live nodes  

3. **Membership Dynamics**
   - Handled **node failures** and **rejoins**, preserving correct routing under churn  
   - Achieved retrieval of **> 99%** of keys even during intermediate failures  

4. **Replication & Consistency**
   - Added **replication factor = 4** (primary + 3 backups)  
   - Ensured **sequential consistency** per key; prioritized consistency under failures  
   - Automated **repair** to re‑replicate lost data after node failures  

5. **Performance Optimization**
   - Tuned **load‑balancing**, **serialization**, and **network I/O**  
   - Deployed across **80 AWS EC2 instances**, benchmarking throughput under 1 000+ clients  

---

## ⚙️ Tools & Technologies

- **Language & Build**: Java 11, Maven  
- **RPC & Serialization**: Protobuf over UDP  
- **Testing & Emulation**:  
  - Custom test clients for functional & failure testing  
  - NetEm for packet loss, delay, and reordering scenarios  
- **Deployment**:  
  - AWS EC2 (Ubuntu), Terraform for infrastructure provisioning  
  - JVM flags (`-Xmx64m`) to enforce memory constraints  
- **Monitoring**: Custom dashboards for throughput and error metrics  

---

## 📐 Architecture & Design

- **Layered Structure**  
  1. **Transport Layer**: UDP request/reply with message‐ID caching  
  2. **Core Logic**: KV operations, consistent hashing, replication, membership  
- **Caching**  
  - Map of `<clientID, messageID> → response` to guarantee at‑most‑once semantics  
- **Consistent Hashing**  
  - 256 ring size with virtual nodes for even load distribution  
  - Successor lookups on the sorted hash ring  
---

