# 05. Distributed Systems & APIs

A unified, comprehensive, production-grade guide to distributed computing theory (MapReduce, CAP/PACELC theorem, Raft/Paxos consensus, Consistent Hashing, Quorum models), distributed networking, and enterprise API integration (REST, GraphQL, gRPC, OAuth2, Webhooks, Token Bucket rate limiting, Reverse ETL).

---

## Architectural Overview: Distributed Network & Integration Architecture

```mermaid
flowchart TD
    subgraph ClientsLayer ["External Clients & SaaS"]
        ClientApp[Enterprise Web / Mobile App]
        SaaSSource[Third-Party SaaS API]
    end

    subgraph GatewayLayer ["API Gateway & Ingress Layer"]
        Gateway["API Gateway / Envoy Proxy<br/>Rate Limiting: Token Bucket<br/>OAuth2 / JWT / mTLS Verification"]
    end

    subgraph ClusterLayer ["Distributed Compute & Storage Cluster"]
        Coordinator["Leader / Coordinator Node<br/>Raft Consensus & Health Heartbeat"]
        Worker1["Worker Node 1<br/>Sharded Data Partition"]
        Worker2["Worker Node 2<br/>Sharded Data Partition"]
        Worker3["Worker Node 3<br/>Sharded Data Partition"]
        
        Coordinator <--> Worker1
        Coordinator <--> Worker2
        Coordinator <--> Worker3
    end

    ClientApp --> Gateway
    SaaSSource --> Gateway
    Gateway --> Coordinator

    style ClientsLayer fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style GatewayLayer fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c084fc
    style ClusterLayer fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style ClientApp fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style SaaSSource fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Gateway fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style Coordinator fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Worker1 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Worker2 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Worker3 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

---


## Table of Contents
1. [Distributed Computing Principles](#1-distributed-computing-principles)
   - [1.1 MapReduce Paradigm](#11-mapreduce-paradigm)
   - [1.2 CAP Theorem](#12-cap-theorem)
   - [1.3 PACELC Theorem](#13-pacelc-theorem)
   - [1.4 Consensus Algorithms (Raft & Paxos)](#14-consensus-algorithms-raft-paxos)
   - [1.5 Distributed Locks](#15-distributed-locks)
   - [1.6 Leader Election & Split-Brain Mitigation](#16-leader-election-split-brain-mitigation)
   - [1.7 Heartbeats & Failure Detection](#17-heartbeats-failure-detection)
2. [Distributed Data Architecture](#2-distributed-data-architecture)
   - [2.1 Horizontal vs Vertical Scaling](#21-horizontal-vs-vertical-scaling)
   - [2.2 Sharding vs Partitioning](#22-sharding-vs-partitioning)
   - [2.3 Consistent Hashing & Virtual Nodes](#23-consistent-hashing-virtual-nodes)
   - [2.4 Database Replication Topologies](#24-database-replication-topologies)
   - [2.5 Replication Lag & Read-After-Write Consistency](#25-replication-lag-read-after-write-consistency)
   - [2.6 Quorum Reads & Writes (W + R > N)](#26-quorum-reads-writes-w-r-n)
   - [2.7 Consistency Spectrum: Strong vs. Eventual Consistency](#27-consistency-spectrum-strong-vs-eventual-consistency)
3. [API Protocols & Communication Architectures](#3-api-protocols-communication-architectures)
   - [3.1 REST vs GraphQL vs gRPC](#31-rest-vs-graphql-vs-grpc)
   - [3.2 HTTP Methods, Status Codes, and Headers](#32-http-methods-status-codes-and-headers)
4. [API Authentication & Security](#4-api-authentication-security)
   - [4.1 API Keys & Bearer Tokens (JWT Structure)](#41-api-keys-bearer-tokens-jwt-structure)
   - [4.2 OAuth 2.0 Flows](#42-oauth-20-flows)
   - [4.3 Mutual TLS (mTLS)](#43-mutual-tls-mtls)
5. [Rate Limiting, Throttling & Pipeline Resilience](#5-rate-limiting-throttling-pipeline-resilience)
   - [5.1 Token Bucket Algorithm](#51-token-bucket-algorithm)
   - [5.2 Leaky Bucket Algorithm](#52-leaky-bucket-algorithm)
   - [5.3 Exponential Backoff with Jitter](#53-exponential-backoff-with-jitter)
6. [Modern Integration Paradigms](#6-modern-integration-paradigms)
   - [6.1 Webhooks vs Long Polling](#61-webhooks-vs-long-polling)
   - [6.2 Webhook Signature Verification](#62-webhook-signature-verification)
   - [6.3 Reverse ETL (Operational Analytics)](#63-reverse-etl-operational-analytics)
   - [6.4 Managed Data Integration Platforms (Fivetran vs Airbyte)](#64-managed-data-integration-platforms-fivetran-vs-airbyte)
7. [References & Further Reading](#7-references-further-reading)

---

# 1. Distributed Computing Principles

---

### 1.1 MapReduce Paradigm

#### Definition
MapReduce is a distributed processing framework formulated by Google for processing massive datasets in parallel across clusters of commodity hardware. It decomposes computation into four sequential phases: **Split $\rightarrow$ Map $\rightarrow$ Shuffle $\rightarrow$ Reduce**.

```mermaid
flowchart TD
    RawData["Raw Input Dataset (100 GB)"] --> S1["Split 1 (64MB)"] & S2["Split 2 (64MB)"] & S3["Split 3 (64MB)"]
    
    subgraph MapPhase ["1. Map Phase (Local Worker Computation)"]
        S1 --> M1["Mapper 1: (Key, Value)"]
        S2 --> M2["Mapper 2: (Key, Value)"]
        S3 --> M3["Mapper 3: (Key, Value)"]
    end

    subgraph ShufflePhase ["2. Shuffle & Sort Phase (All-to-All Network Transfer)"]
        M1 & M2 & M3 --> Shuffle["Network Partition & Sort by Key"]
    end

    subgraph ReducePhase ["3. Reduce Phase (Aggregate Matching Keys)"]
        Shuffle --> R1["Reducer 1: Key Group A"]
        Shuffle --> R2["Reducer 2: Key Group B"]
    end

    R1 & R2 --> Out[Consolidated Final Output]

    style MapPhase fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style ShufflePhase fill:#0f172a,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style ReducePhase fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style RawData fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style S1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style S2 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style S3 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style M1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style M2 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style M3 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Shuffle fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style R1 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style R2 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Out fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
```

#### References
- [Google Research: MapReduce Paper (Dean & Ghemawat)](https://research.google/pubs/pub62/)
- [GeeksforGeeks: MapReduce in Hadoop](https://www.geeksforgeeks.org/hadoop-mapreduce/)

---

### 1.2 CAP Theorem

#### Definition
Formulated by Eric Brewer, the CAP Theorem proves that a distributed data store can guarantee at most **two out of the following three guarantees** simultaneously in the presence of network partitions:
1. **Consistency (C):** Every read receives the most recent write or an error (linearizability).
2. **Availability (A):** Every non-failing node returns a non-error response for every request (without guaranteeing it is the freshest write).
3. **Partition Tolerance (P):** The system continues operating despite arbitrary network packet drops or server communication splits.

```mermaid
flowchart TD
    P["Partition Tolerance (P)<br/>(Mandatory in Real Networks!)"]
    
    subgraph CP_Systems ["CP Systems (Consistency > Availability)"]
        CP["HBase, MongoDB, Bigtable, ZooKeeper<br/>(Rejects reads/writes during network split to prevent stale data)"]
    end

    subgraph AP_Systems ["AP Systems (Availability > Consistency)"]
        AP["Cassandra, DynamoDB, CouchDB<br/>(Serves stale reads during split; reconciles eventually)"]
    end

    P --> CP
    P --> AP

    style CP_Systems fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style AP_Systems fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style P fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style CP fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style AP fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### The Reality: Network Partitions are Inevitable
Because physical network cables, switches, and cloud availability zones can experience transient network partitions ($P$), **a distributed system cannot choose "CA"**. The real-world choice is strictly:
- **CP (Consistency / Partition Tolerant):** Halt or fail writes on disconnected nodes until partition heals.
- **AP (Availability / Partition Tolerant):** Accept writes on both sides; reconcile conflicting versions later.

#### References
- [GeeksforGeeks: CAP Theorem in DBMS](https://www.geeksforgeeks.org/the-cap-theorem-in-dbms/)
- [DataCamp: Understanding the CAP Theorem](https://www.datacamp.com/tutorial/cap-theorem)

---

### 1.3 PACELC Theorem

#### Definition
An extension of the CAP Theorem formulated by Professor Daniel Abadi. The CAP Theorem only describes system behavior during rare network partitions ($P$). PACELC addresses system behavior during the $99.99\%$ of time when the network is functioning normally ($E$ - Else):
- **If there is a Partition ($P$):** Trade off **Availability ($A$)** versus **Consistency ($C$)**.
- **Else ($E$ - Normal Operation):** Trade off **Latency ($L$)** versus **Consistency ($C$)**.

$$\text{If } \mathbf{P} \implies (\mathbf{A} \lor \mathbf{C}) \quad \mathbf{ELSE} \implies (\mathbf{L} \lor \mathbf{C})$$

```mermaid
flowchart TD
    State{Is Cluster Partitioned?}
    State -- Yes: P Mode --> CAPChoice{Choose Trade-off}
    CAPChoice -- Availability --> PA["PA: Serve stale reads, accept writes\n(DynamoDB, Cassandra)"]
    CAPChoice -- Consistency --> PC["PC: Reject writes on partitioned nodes\n(Spanner, HBase, ZooKeeper)"]

    State -- No: Else Mode --> NormalChoice{Choose Trade-off}
    NormalChoice -- Low Latency --> EL["EL: Return single-node local read\n(DynamoDB, MongoDB uncommitted)"]
    NormalChoice -- Strict Consistency --> EC["EC: Synchronous cross-node replication\n(Spanner TrueTime, Postgres Sync Replica)"]

    style State fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style CAPChoice fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style PA fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style PC fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style NormalChoice fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style EL fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style EC fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
```

#### Practical Use Cases & System Classifications
- **PC/EC (Google Spanner, CockroachDB, Bigtable)**: Under partition, guarantees consistency ($PC$). Under normal operation, sacrifices latency ($EC$) by executing synchronous two-phase commits across multi-region quorums. Ideal for financial core ledgers.
- **PA/EL (Amazon DynamoDB, Apache Cassandra, Couchbase)**: Under partition, remains available ($PA$). In normal operation, sacrifices consistency for sub-5ms latency ($EL$) by writing to a local node and replicating asynchronously. Ideal for e-commerce shopping carts and clickstreams.

#### Code Example: Tuning PACELC Trade-Offs in Client Queries
```python
# Demonstrating PACELC latency vs consistency control in Python (Cassandra / DynamoDB style)
from typing import Literal

class DistributedClient:
    def __init__(self, cluster_type: Literal["PA_EL", "PC_EC"]):
        # Store cluster design strategy:
        # PA_EL: Partition -> Availability, Else -> Low Latency (e.g., DynamoDB, Cassandra)
        # PC_EC: Partition -> Consistency, Else -> Strict Consistency (e.g., Google Spanner)
        self.cluster_type = cluster_type

    def read_record(self, key: str, consistency_level: str) -> dict:
        """
        Queries the distributed key-value store under specified consistency guarantees.
        consistency_level options:
          - 'ONE': Lowest latency (EL), reads from nearest replica, may return stale data.
          - 'QUORUM': High consistency (EC), queries majority of replicas (N/2 + 1), higher network latency.
        """
        # Level ONE routes query to the closest single storage node without cross-checking peers
        if consistency_level == "ONE":
            # Simulate sub-millisecond local replica response time
            print(f"[{self.cluster_type}] Reading {key} with Level=ONE -> Latency: 2ms (Eventual)")
            # Return cached or local replica state (version 41 may lag behind primary version 42)
            return {"key": key, "val": "cached_data", "version": 41}

        # Level QUORUM contacts majority of replicas (floor(N/2) + 1) to resolve latest timestamp
        elif consistency_level == "QUORUM":
            # Incur network round-trip delay to negotiate consensus across geographically distributed nodes
            print(f"[{self.cluster_type}] Reading {key} with Level=QUORUM -> Latency: 28ms (Strong)")
            # Return guaranteed latest version reconciled via read repair or timestamp check
            return {"key": key, "val": "freshest_data", "version": 42}

        # Raise explicit error for unsupported consistency level requests
        raise ValueError(f"Unknown level: {consistency_level}")

# PA/EL Client prioritizes speed over linearizability
dynamo_client = DistributedClient("PA_EL")
# Fast read serves user profile from nearest replica with 2ms latency
record_fast = dynamo_client.read_record("user:101", consistency_level="ONE")

# PC/EC Client prioritizes serializable correctness over latency
spanner_client = DistributedClient("PC_EC")
# Strict read waits 28ms for multi-region quorum consensus before confirming value
record_strict = spanner_client.read_record("user:101", consistency_level="QUORUM")
```

#### References
- [Daniel Abadi's Blog: Consistency Tradeoffs in Modern Distributed Database System Design](http://dbmsmusings.blogspot.com/2010/04/problems-with-cap-and-yahoos-little.html)
- [GeeksforGeeks: PACELC Theorem](https://www.geeksforgeeks.org/pacelc-theorem/)

---

### 1.4 Consensus Algorithms (Raft & Paxos)

#### Definition
Consensus algorithms ensure that a distributed cluster of nodes agree on a shared state or log of events, even when some nodes crash or experience network delays.

```mermaid
flowchart TD
    subgraph ClusterMembership ["Raft Consensus Node Roles"]
        FollowerNode["FOLLOWER Node<br/>(Passively responds to Leader heartbeats & Candidate votes)"]
        CandidateNode["CANDIDATE Node<br/>(Increments term, votes for self, solicits quorum votes)"]
        LeaderNode["LEADER Node<br/>(Handles all client writes, coordinates log replication)"]
    end

    FollowerNode -->|"Election timeout fires without heartbeat (150-300ms)"| CandidateNode
    CandidateNode -->|"Receives votes from majority quorum (> N/2)"| LeaderNode
    CandidateNode -->|"Discovers legitimate leader or higher term"| FollowerNode
    LeaderNode -->|"Discovers higher term in cluster network"| FollowerNode

    style ClusterMembership fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style FollowerNode fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style CandidateNode fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style LeaderNode fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Key Elements of Raft Consensus
1. **Leader Election:** If a Follower receives no heartbeats within an election timeout (150-300ms), it transitions to **Candidate** and requests votes. If it wins a majority vote ($> N/2$), it becomes the **Leader**.
2. **Log Replication:** The Leader accepts client writes, writes to its local log, replicates entries to Follower nodes, and commits the write once a quorum confirms receipt.
3. **Quorum Rule:** A cluster of $N$ nodes can tolerate $F$ failures where $N = 2F + 1$ (e.g., a 5-node cluster survives 2 dead nodes).

#### References
- [The Raft Consensus Algorithm](https://raft.github.io/)
- [GeeksforGeeks: Consensus Algorithms in Distributed Systems](https://www.geeksforgeeks.org/consensus-algorithms-in-distributed-systems/)

---

### 1.5 Distributed Locks

#### Definition
Distributed Locks ensure that mutually exclusive access to a shared resource (e.g., executing a non-reentrant batch pipeline, updating an inventory ledger) is granted to only one worker process across an entire cluster.

#### Implementation Mechanisms
1. **ZooKeeper Ephemeral Sequential Nodes:** Clients create an ephemeral znode. The client with the lowest sequence number holds the lock; others watch the preceding node. If the holder crashes, its session drops and the lock automatically releases.
2. **Redis Redlock Algorithm:** A client acquires locks across $M$ independent Redis master instances using millisecond-precision timestamps. If acquired on majority ($> M/2$) instances within timeout, the lock is valid.

```python
# Redis Distributed Lock Pattern in Python (Atomic Redlock Lease Primitive)
import redis
import time

# Establish connection pool to Redis cluster
r = redis.Redis(host='redis-server', port=6379, decode_responses=True)

# Attempt atomic lock acquisition with lease safety:
# - 'nx=True': Only set key if it does NOT exist (mutual exclusion guarantee)
# - 'px=30000': Set automatic TTL expiration to 30,000 ms (prevents deadlock if worker process crashes)
# - Value 'worker_node_42': Unique owner identifier ensuring only the lock holder can release it
lock_acquired = r.set("lock:daily_etl_job", "worker_node_42", nx=True, px=30000)

if lock_acquired:
    try:
        # Critical section: exactly one worker executes this pipeline concurrently across the fleet
        print("Acquired lease! Executing critical ETL pipeline...")
        time.sleep(1.0) # Simulate pipeline execution
    finally:
        # Atomic lock release via Lua script evaluated on Redis server:
        # Prevents releasing the lock if execution took > 30s and another worker acquired a new lease!
        release_lua = '''
        if redis.call("get", KEYS[1]) == ARGV[1] then
            return redis.call("del", KEYS[1])
        else
            return 0
        end
        '''
        # Execute Lua script atomically (1 key passed, key name, client token)
        released = r.eval(release_lua, 1, "lock:daily_etl_job", "worker_node_42")
        print("Lock safely released:", bool(released))
else:
    print("Lock held by another worker. Backing off to prevent concurrent mutation.")
```

#### References
- [Redis Documentation: Distributed Locks with Redlock](https://redis.io/docs/manual/patterns/distributed-locks/)
- [GeeksforGeeks: Distributed Locks](https://www.geeksforgeeks.org/distributed-locks/)

---

### 1.6 Leader Election & Split-Brain Mitigation

#### Definition
The **Split-Brain Problem** occurs when a network partition bisects a cluster into two disconnected halves, and each half independently elects its own "Leader", causing data divergence and catastrophic state corruption.

```mermaid
flowchart TD
    subgraph NetworkSplit ["Network Partition Severed"]
        subgraph SubClusterA ["Partition A (2 Nodes) - Minority Split"]
            direction LR
            Node1["Node 1 (Believes it is Leader)"] --- Node2["Node 2"]
        end

        subgraph SubClusterB ["Partition B (3 Nodes) - HAS QUORUM!"]
            direction LR
            Node3["Node 3 (Elected New Leader)"] --- Node4["Node 4"] --- Node5["Node 5"]
        end
    end

    Node1 -.->|"Quorum Check Fails (2 < 3)"| StepDown["Node 1 MUST Step Down!"]
    Node3 -->|"Majority Won (3 > 2.5)"| ValidLeader["Node 3 Operates as True Leader"]

    style NetworkSplit fill:#0f172a,stroke:#ef4444,stroke-width:1.5px,stroke-dasharray: 5 5,color:#fca5a5
    style SubClusterA fill:#1e293b,stroke:#ef4444,stroke-width:1.5px,color:#fca5a5
    style SubClusterB fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#86efac

    style Node1 fill:#182234,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style Node2 fill:#182234,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style Node3 fill:#182234,stroke:#10b981,stroke-width:1.5px,color:#fff
    style Node4 fill:#182234,stroke:#10b981,stroke-width:1.5px,color:#fff
    style Node5 fill:#182234,stroke:#10b981,stroke-width:1.5px,color:#fff
    style StepDown fill:#7f1d1d,stroke:#ef4444,stroke-width:1.5px,color:#fecaca
    style ValidLeader fill:#064e3b,stroke:#10b981,stroke-width:1.5px,color:#a7f3d0
```

#### Mitigation: Fencing Tokens & Quorums
- **Strict Quorum Requirement:** A candidate can only become leader if it receives votes from $\lfloor N/2 
floor + 1$ nodes. The minor partition ($2 < 3$) can never form a quorum and must step down.
- **Fencing Tokens:** Every newly elected leader receives a monotonically increasing token (e.g., Token 43). Any write request sent to shared storage with an older token (Token 42 from a zombie leader) is rejected.

---

### 1.7 Heartbeats & Failure Detection

#### Definition
A mechanism where distributed cluster nodes periodically transmit lightweight telemetry signals (**Heartbeats**) to a coordinator or peer cluster nodes (e.g., every 1 second). If a node fails to send a heartbeat within an agreed-upon lease timeout window (e.g., 5 seconds), the **Failure Detector** marks the node as `DEAD` and triggers automated failover, partition reassignment, and re-replication.

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'actorBkg': '#1e293b', 'actorBorder': '#3b82f6', 'actorTextColor': '#ffffff', 'signalColor': '#93c5fd', 'signalTextColor': '#ffffff', 'noteBkgColor': '#1e293b', 'noteBorderColor': '#f59e0b', 'noteTextColor': '#fde68a', 'activationBkgColor': '#3b82f6', 'sequenceNumberColor': '#ffffff' }}}%%
sequenceDiagram
    autonumber
    actor Worker as Worker Node 3
    participant Coord as Cluster Coordinator (Leader)

    loop Every 1000ms (Healthy State)
        Worker->>Coord: Heartbeat Ping {node_id: "w3", cpu_util: 35%, timestamp}
        Coord-->>Worker: Heartbeat ACK {lease_renewed_until: +5000ms}
    end

    Note over Worker: Worker crashes / Network cable severed
    Note over Coord: Timer ticking... 1s... 3s... 5s (Timeout Expired!)
    Coord->>Coord: Mark Node "w3" as DEAD
    Coord->>Coord: Trigger partition rebalance: reassign w3 tasks to w1 and w2
```

#### Code Example: Failure Detector with Heartbeat Tracking
```python
import time
from typing import Dict, List, Set

class NodeHealthMonitor:
    """
    Tracks distributed heartbeat pings from worker nodes to detect silent crashes,
    network partitions, or GC pauses, triggering cluster partition rebalances.
    """
    def __init__(self, timeout_seconds: float = 3.0):
        # Maximum allowed gap between heartbeats before declaring node dead
        self.timeout_seconds = timeout_seconds
        # Registry tracking epoch timestamp of last received heartbeat per node ID
        self.last_heartbeats: Dict[str, float] = {}
        # Set maintaining active cluster membership roster
        self.active_nodes: Set[str] = set()

    def receive_heartbeat(self, node_id: str) -> None:
        # Record arrival timestamp
        now = time.time()
        self.last_heartbeats[node_id] = now
        # If node was previously unregistered or reconnecting after partition, admit to cluster
        if node_id not in self.active_nodes:
            self.active_nodes.add(node_id)
            print(f"[CLUSTER] Node {node_id} JOINED cluster.")

    def check_for_failures(self) -> List[str]:
        now = time.time()
        dead_nodes: List[str] = []
        # Inspect snapshot of active roster to safely allow mutation during iteration
        for node_id in list(self.active_nodes):
            elapsed = now - self.last_heartbeats.get(node_id, 0.0)
            # Evaluate heartbeat threshold expiration
            if elapsed > self.timeout_seconds:
                print(f"[ALERT] Node {node_id} TIMED OUT ({elapsed:.2f}s elapsed)! Marking DEAD.")
                # Evict from active set to prevent routing further traffic
                self.active_nodes.remove(node_id)
                # Mark for task partition rebalancing across healthy workers
                dead_nodes.append(node_id)
        return dead_nodes

# Initialize monitor with strict 500ms timeout threshold for rapid failover
monitor = NodeHealthMonitor(timeout_seconds=0.5)
# Register workers 1 and 2 upon initial cluster boot
monitor.receive_heartbeat("worker-1")
monitor.receive_heartbeat("worker-2")

# Advance clock within healthy lease window
time.sleep(0.2)
# worker-1 sends expected keepalive pulse; worker-2 misses its pulse (crashed / partitioned)
monitor.receive_heartbeat("worker-1")

# Sleep beyond the 500ms lease duration
time.sleep(0.4)
# Run failure detector sweep: evicts worker-2 and outputs reassignment trigger
failed = monitor.check_for_failures()
print("Dead nodes triggering reassignment:", failed)
```

#### References
- [GeeksforGeeks: Failure Detection in Distributed Systems](https://www.geeksforgeeks.org/failure-detection-in-distributed-systems/)
- [Apache Kafka Broker Liveness & Heartbeat Thread](https://kafka.apache.org/documentation/#design_ha)

---

# 2. Distributed Data Architecture

---

### 2.1 Horizontal vs Vertical Scaling

| Dimension | Vertical Scaling (Scale-Up) | Horizontal Scaling (Scale-Out) |
| :--- | :--- | :--- |
| **Approach** | Add more RAM, CPU, NVMe SSDs to 1 server | Add more commodity servers to the cluster |
| **Hardware Limit** | Hard ceiling (e.g., max 128 cores, 4TB RAM) | Virtually infinite capacity (10,000+ nodes) |
| **Complexity** | Zero distributed architecture complexity | Complex network sharding, consensus, shuffles |
| **Cost Curve** | Exponential (Supercomputers are costly) | Linear commodity hardware pricing |

---

### 2.2 Sharding vs Partitioning

#### Definitions & Key Differences
- **Partitioning**: Splitting a large table into smaller physical chunks residing **on the same physical database instance** (e.g., PostgreSQL range partitioning by month). Improves query performance through partition pruning on a single host.
- **Sharding**: Distributing distinct slices of data across **completely separate physical database servers / network nodes** (e.g., MongoDB, Spanner, Citus). Overcomes hardware limits (CPU, RAM, disk IOPS) of a single server.

```mermaid
flowchart TD
    subgraph SingleHost ["Table Partitioning (Single Host)"]
        T[Orders Table] --> P1["Partition 2026-Q1 (File 1)"]
        T --> P2["Partition 2026-Q2 (File 2)"]
    end

    subgraph DistributedCluster ["Database Sharding (Multiple Network Hosts)"]
        Router{Sharding Router}
        Router -->|"hash(key) in [0, 33]"| Shard1["Physical DB Node 1 (IP: 10.0.1.10)"]
        Router -->|"hash(key) in [34, 66]"| Shard2["Physical DB Node 2 (IP: 10.0.1.11)"]
        Router -->|"hash(key) in [67, 100]"| Shard3["Physical DB Node 3 (IP: 10.0.1.12)"]
    end

    style SingleHost fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style DistributedCluster fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style T fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style P1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style P2 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Router fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style Shard1 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Shard2 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Shard3 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Code Example: PostgreSQL Partitioning SQL & Python Shard Routing
```sql
-- 1. PostgreSQL Table Partitioning (Single Database Host, Range-Based)
-- Define the parent partitioned table with range partitioning key
CREATE TABLE customer_orders (
    order_id BIGSERIAL,               -- Surrogate primary key auto-incremented
    customer_id INT NOT NULL,         -- Customer foreign identifier
    order_date DATE NOT NULL,         -- Partition boundary key (Range)
    total_amount NUMERIC(12, 2)       -- Order value
) PARTITION BY RANGE (order_date);

-- Create physical partition child table for Quarter 1
CREATE TABLE orders_2026_q1 PARTITION OF customer_orders
    FOR VALUES FROM ('2026-01-01') TO ('2026-04-01');

-- Create physical partition child table for Quarter 2
CREATE TABLE orders_2026_q2 PARTITION OF customer_orders
    FOR VALUES FROM ('2026-04-01') TO ('2026-07-01');
-- Benefit: Single host planner performs Partition Pruning, scanning only relevant partition files
```

```python
# 2. Application-Level Shard Router (Across Multiple Physical Database Hosts)
import hashlib
from typing import List

class DatabaseShardRouter:
    """
    Deterministic hash modulo router directing client read/write queries
    to distinct physical database nodes based on sharding key.
    """
    def __init__(self, shard_hosts: List[str]):
        # Store inventory of physical database server endpoints
        self.shard_hosts = shard_hosts

    def get_shard_host(self, customer_id: int) -> str:
        # Compute uniform MD5 digest of the customer identifier string
        digest = hashlib.md5(str(customer_id).encode("utf-8")).hexdigest()
        # Convert hex digest string into 128-bit integer for numerical modulo
        hash_val = int(digest, 16)
        # Distribute keys uniformly across shard cluster size using modulo
        shard_idx = hash_val % len(self.shard_hosts)
        # Return targeted physical host connection string
        return self.shard_hosts[shard_idx]

# Configure cluster with 3 distinct physical PostgreSQL hosts
router = DatabaseShardRouter(["db-node-1.internal", "db-node-2.internal", "db-node-3.internal"])
# Route customer 101 deterministically to physical node
print("Customer 101 routed to:", router.get_shard_host(101))
# Route customer 102 deterministically to physical node
print("Customer 102 routed to:", router.get_shard_host(102))
```

#### References
- [GeeksforGeeks: Database Sharding vs Partitioning](https://www.geeksforgeeks.org/database-sharding-vs-partitioning/)

---

### 2.3 Consistent Hashing & Virtual Nodes

#### Definition
In traditional modular hashing (`hash(key) % N`), adding or removing a single database node ($N 
ightarrow N+1$) causes almost **100% of all keys to rehash to new nodes**, triggering massive network re-shuffling storms.

**Consistent Hashing** maps both data keys and physical nodes onto an abstract circular integer ring ($0$ to $2^{32}-1$). A key is assigned to the first node encountered moving clockwise around the ring.

```mermaid
flowchart TD
    subgraph HashRing ["Consistent Hashing Ring (0 to 2^32 - 1)"]
        N1["Node A (Pos: 100)"]
        N2["Node B (Pos: 1,000)"]
        N3["Node C (Pos: 5,000)"]
        
        K1["Key 'User_99' (Hash: 450)"] -->|Clockwise Next| N2
        K2["Key 'Order_12' (Hash: 2,500)"] -->|Clockwise Next| N3
    end

    style HashRing fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd

    style N1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style N2 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style N3 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style K1 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style K2 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Why Consistent Hashing is Superior
When a new node joins or leaves, **only $K/N$ keys need to be migrated** (where $K$ is total keys and $N$ is total nodes). **Virtual Nodes (V-Nodes)** replicate each physical server at multiple locations on the ring to prevent hot-spot skew.

#### References
- [GeeksforGeeks: Consistent Hashing](https://www.geeksforgeeks.org/consistent-hashing/)
- [System Design Primer: Consistent Hashing](https://github.com/donnemartin/system-design-primer#consistent-hashing)

---

### 2.4 Database Replication Topologies

#### 1. Single-Leader (Primary-Replica)
All writes are directed to a single Primary Leader node. The Leader streams replication logs to read-only Follower replicas. Reads can be served by any replica.
- **Engines:** PostgreSQL, MySQL, Redis.

#### 2. Multi-Leader (Active-Active)
Multiple nodes accept writes simultaneously and asynchronously synchronize deltas.
- **Use Case:** Multi-region active-active deployments (e.g., US datacenter and EU datacenter). Requires conflict resolution strategies (Last-Write-Wins, CRDTs).

#### 3. Leaderless (Dynamo-Style)
Any node can accept reads and writes. Clients write to multiple nodes concurrently using Quorum consensus.
- **Engines:** Apache Cassandra, Amazon DynamoDB, ScyllaDB.

```mermaid
flowchart TD
    subgraph SingleLeader ["1. Single-Leader (Postgres)"]
        W1[Client Write] --> Leader1[Primary Node]
        Leader1 -->|Replication Log| Rep1[Replica 1]
        Leader1 -->|Replication Log| Rep2[Replica 2]
    end

    subgraph Leaderless ["2. Leaderless (Cassandra Quorum)"]
        W2[Client Write] --> N_A[Node A]
        W2 --> N_B[Node B]
        W2 --> N_C[Node C]
    end

    style SingleLeader fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Leaderless fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style W1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Leader1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Rep1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Rep2 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style W2 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style N_A fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style N_B fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style N_C fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### References
- [Martin Kleppmann: Designing Data-Intensive Applications (DDIA)](https://dataintensive.net/)
- [GeeksforGeeks: Replication in DBMS](https://www.geeksforgeeks.org/database-replication/)

---

### 2.5 Replication Lag & Read-After-Write Consistency

#### The Asynchronous Replication Hazard
When a user updates their profile picture, the write commits on the Leader. If the user immediately refreshes their browser, the read request may route to an asynchronous Replica that is lagging 500ms behind, displaying the old picture.

#### Read-After-Write Consistency Guarantee
Guarantees that if a user reloads a page after making a modification, **their own updates are guaranteed to be visible to them**:
- Route reads for the user's *own* profile to the Leader for 60 seconds after a write.
- Track client commit timestamps (or Log Sequence Numbers - LSN) and only route reads to replicas whose replication offset $\ge$ commit timestamp.

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'actorBkg': '#1e293b', 'actorBorder': '#3b82f6', 'actorTextColor': '#ffffff', 'signalColor': '#93c5fd', 'signalTextColor': '#ffffff', 'noteBkgColor': '#1e293b', 'noteBorderColor': '#f59e0b', 'noteTextColor': '#fde68a', 'activationBkgColor': '#3b82f6', 'sequenceNumberColor': '#ffffff' }}}%%
sequenceDiagram
    autonumber
    actor User
    participant Router as Read/Write Router
    participant Leader as Primary DB (Leader)
    participant Replica as Read Replica (Lagging 200ms)

    User->>Router: POST /profile/update {avatar: "new.png"}
    Router->>Leader: Write to Primary DB (LSN: 5042)
    Leader-->>Router: Write Committed (LSN: 5042)
    Router-->>User: 200 OK (Set Cookie: last_write_lsn=5042)

    User->>Router: GET /profile (Cookie: last_write_lsn=5042)
    Router->>Replica: Query LSN status?
    Replica-->>Router: Current Replica LSN: 5039 (Behind!)
    Note over Router: Replica is stale! Fallback to Leader
    Router->>Leader: Read /profile from Primary
    Leader-->>User: Profile with fresh avatar "new.png"
```

#### Code Example: Read-After-Write Routing Client
```python
from typing import Dict, Any, Optional

class ReadAfterWriteRouter:
    """
    Guarantees 'Read-Your-Own-Writes' consistency in an asynchronous replica topology
    by comparing client write Log Sequence Number (LSN) against replica LSN.
    """
    def __init__(self):
        # Monotonically increasing Log Sequence Number on primary leader
        self.primary_lsn = 1000
        # Replica LSN showing current replication replay progress (lagging behind)
        self.replica_lsn = 995
        # Primary storage engine holding authoritative state
        self.primary_store: Dict[str, Any] = {"user:42": "Original Name"}
        # Read-replica storage engine holding asynchronously replayed state
        self.replica_store: Dict[str, Any] = {"user:42": "Original Name"}

    def write(self, key: str, value: Any) -> int:
        # Increment write WAL sequence number atomically
        self.primary_lsn += 1
        # Commit updated record to primary state
        self.primary_store[key] = value
        print(f"[PRIMARY] Write committed at LSN={self.primary_lsn}")
        # Return commit token (LSN) to client session for subsequent reads
        return self.primary_lsn

    def read(self, key: str, client_last_write_lsn: Optional[int] = None) -> Any:
        # If client performed a prior write and replica has not caught up to that LSN:
        if client_last_write_lsn and client_last_write_lsn > self.replica_lsn:
            # Fall back to reading from Primary to prevent stale reads
            print(f"[ROUTER] Replica is lagging ({self.replica_lsn} < {client_last_write_lsn}). Routing read to PRIMARY.")
            return self.primary_store.get(key)
        # If replica has caught up or client made no recent writes, offload read to replica
        print(f"[ROUTER] Replica is up to date ({self.replica_lsn}). Serving read from REPLICA.")
        return self.replica_store.get(key)

client = ReadAfterWriteRouter()
# User submits profile update: receives transaction commit token (LSN=1001)
commit_token = client.write("user:42", "Updated VIP Name")
# Client immediately reloads profile page: token routes request to Primary, avoiding stale replica!
result = client.read("user:42", client_last_write_lsn=commit_token)
print("Read result:", result)
```

---

### 2.6 Quorum Reads & Writes ($W + R > N$)

#### Definition
In leaderless systems, strong consistency is guaranteed mathematically using Quorum consensus across $N$ total replica copies:
- $N$ = Number of replication nodes.
- $W$ = Number of nodes that must acknowledge a **Write** before it is declared successful.
- $R$ = Number of nodes that must respond to a **Read** request.

$$\mathbf{W} + \mathbf{R} > \mathbf{N}$$

```mermaid
flowchart LR
    subgraph QuorumMath ["Quorum Overlap: W=2, R=2, N=3"]
        Node1["Node 1 (Write Acked: v2)"]
        Node2["Node 2 (Write Acked: v2)"]
        Node3["Node 3 (Old Version: v1)"]
    end

    ClientRead["Client Read (R=2): Reads Node 2 & Node 3"] --> Overlap["Guaranteed to inspect at least one node with v2! (Strong Consistency)"]

    style QuorumMath fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd

    style Node1 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Node2 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Node3 fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style ClientRead fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Overlap fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
```

#### If $W + R > N$:
The set of read nodes and the set of write nodes **must overlap on at least one node**. The client reads version timestamps and selects the highest version, guaranteeing **Strong Consistency**.

---

### 2.7 Consistency Spectrum: Strong vs. Eventual Consistency

| Consistency Level | Definition | Engine Examples |
| :--- | :--- | :--- |
| **Strong Consistency (Linearizability)** | Every read immediately reflects the latest committed write globally. Zero stale data tolerated. | Google Spanner, CockroachDB, RDBMS Leader |
| **Causal Consistency** | Operations causally related (question followed by reply) are observed in identical order everywhere. | MongoDB causal sessions |
| **Eventual Consistency** | If no new updates are made, all replicas will eventually converge to the same value over time. High availability, ultra-low latency. | Amazon S3, Cassandra, DynamoDB |

#### References
- [GeeksforGeeks: Eventual Consistency](https://www.geeksforgeeks.org/eventual-consistency/)
- [DataCamp: Distributed Database Concepts](https://www.datacamp.com/tutorial/database-engines)

---

---

---
# 3. API Protocols & Communication Architectures

---

### 3.1 REST vs GraphQL vs gRPC

#### Comparison Matrix
| Architectural Dimension | REST (Representational State Transfer) | GraphQL | gRPC (Google Remote Procedure Call) |
| :--- | :--- | :--- | :--- |
| **Protocol** | HTTP 1.1 / HTTP 2 | HTTP 1.1 / HTTP 2 | **HTTP/2 (Multiplexed Streams)** |
| **Payload Format** | JSON (Text-based) | JSON (Query-specified) | **Protocol Buffers (Binary Protobuf)** |
| **Data Fetching** | Over-fetching / Under-fetching | **Exact field fetching (No over-fetching)** | Typed RPC methods |
| **Performance** | Moderate | Moderate (Query parsing overhead) | **Extremely Fast (10x faster than REST)** |
| **Best Used For** | Public SaaS APIs, CRUD endpoints | Mobile apps, dynamic UI dashboards | **Inter-service microservice streaming** |

```mermaid
flowchart TD
    subgraph REST_Fetch ["1. REST API (Fixed Schema, Over-Fetching)"]
        Client1[Client] -->|"GET /users/1"| S1[Server]
        S1 -->|Returns all 40 columns in JSON| Client1
    end

    subgraph GraphQL_Fetch ["2. GraphQL (Client Controls Payload Shape)"]
        Client2[Client] -->|"POST: query { user(id: 1) { name email } }"| S2[Server]
        S2 -->|Returns ONLY name and email in JSON| Client2
    end

    subgraph gRPC_Fetch ["3. gRPC (High-Speed Binary Protobuf on HTTP/2)"]
        Client3[Microservice Client] -->|"GetUser(UserRequest)"| S3[gRPC Server]
        S3 -->|Binary Protobuf Byte Stream| Client3
    end

    style REST_Fetch fill:#0f172a,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style GraphQL_Fetch fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c084fc
    style gRPC_Fetch fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style Client1 fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style S1 fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style Client2 fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style S2 fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style Client3 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style S3 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### References
- [GeeksforGeeks: REST vs GraphQL vs gRPC](https://www.geeksforgeeks.org/difference-between-rest-api-and-graphql/)
- [DataCamp: Understanding Web APIs](https://www.datacamp.com/tutorial/what-is-an-api)

---

### 3.2 HTTP Methods, Status Codes, and Headers

#### Methods
- `GET`: Safe, idempotent read.
- `POST`: Non-idempotent resource creation.
- `PUT`: Idempotent full resource replacement.
- `PATCH`: Partial resource update.
- `DELETE`: Idempotent resource removal.

#### Critical Status Codes for Pipelines
- `200 OK` / `201 Created`: Request succeeded.
- `400 Bad Request`: Schema validation error.
- `401 Unauthorized` / `403 Forbidden`: Token invalid or permissions missing.
- `429 Too Many Requests`: Rate limit breached (**Trigger Backoff!**).
- `500 Internal Server Error` / `503 Service Unavailable`: Upstream crash (**Retryable**).

---

# 4. API Authentication & Security

---

### 4.1 API Keys & Bearer Tokens (JWT Structure)

#### JSON Web Token (JWT) Anatomy
A JWT is a compact, URL-safe string structured as:
$$\text{Header (Algorithm)} \; . \; \text{Payload (Claims/User ID/Expiry)} \; . \; \text{Signature (Cryptographic HMAC)}$$

```json
    // "header": 
    {
        "alg": "HS256",
        "typ": "JWT"
    }
    // "payload": 
    {
        "sub": "user_12345",
        "iat": 1700000000,
        "exp": 1700003600,
        "roles": ["admin", "data_ingest"]
    }
    // "signature": "a-string-secret-at-least-256-bits-long"

// Gets Encoded as: 
// eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyXzEyMzQ1IiwiaWF0IjoxNzAwMDAwMDAwLCJleHAiOjE3MDAwMDM2MDAsInJvbGVzIjpbImFkbWluIiwiZGF0YV9pbmdlc3QiXX0.DWkrkUkOXitp6TASupJi-vV7l7C84yUiPWAZ4_cDO0k
```

---

### 4.2 OAuth 2.0 Flows

#### Client Credentials Flow (Machine-to-Machine Pipelines)
Used by automated data ingestion jobs to authenticate without user interaction.

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'actorBkg': '#1e293b', 'actorBorder': '#3b82f6', 'actorTextColor': '#ffffff', 'signalColor': '#93c5fd', 'signalTextColor': '#ffffff', 'noteBkgColor': '#1e293b', 'noteBorderColor': '#f59e0b', 'noteTextColor': '#fde68a', 'activationBkgColor': '#3b82f6', 'sequenceNumberColor': '#ffffff' }}}%%
sequenceDiagram
    participant Pipeline as Data Ingestion Pipeline
    participant AuthServer as OAuth 2.0 Token Server
    participant SaaS as Salesforce / Stripe API

    Pipeline->>AuthServer: POST /token (grant_type=client_credentials, client_id, client_secret)
    AuthServer->>AuthServer: Authenticate Credentials
    AuthServer-->>Pipeline: 200 OK {access_token: "eyJhbGci...", expires_in: 3600}
    Pipeline->>SaaS: GET /v1/customers (Headers: Authorization: Bearer <access_token>)
    SaaS-->>Pipeline: 200 OK {Customer Data...}
```

---

### 4.3 Mutual TLS (mTLS)

Standard TLS validates only the server's identity. In **mTLS**, both the client and server present X.509 cryptographic certificates to mutually authenticate one another before establishing a secure TCP connection.

---

# 5. Rate Limiting, Throttling & Pipeline Resilience

---

### 5.1 Token Bucket Algorithm

#### Definition
Tokens are continuously added to a bucket at a fixed fill rate ($r$ tokens/second) up to a max capacity ($b$). Each incoming request must consume 1 token. If the bucket is empty, requests are throttled (HTTP 429). **Supports short bursts of traffic**.

```mermaid
flowchart TD
    Refill["Refill: Adds r Tokens Every Second"] --> Bucket["Token Bucket (Max Capacity: 100 Tokens)"]
    Request["Incoming API Request"] --> Check{"Token Available in Bucket?"}
    Bucket --> Check
    Check -- Yes --> Consume["Consume 1 Token -> Execute Request"]
    Check -- No --> Drop["Reject Request with HTTP 429 Too Many Requests!"]

    style Refill fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Bucket fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Request fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Check fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Consume fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Drop fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
```

---

### 5.2 Leaky Bucket Algorithm

#### Definition
Requests enter a buffer (the bucket) at arbitrary burst rates and leak out at a **constant, strictly regulated rate**. If the buffer capacity overflows, excess requests are dropped or rejected with HTTP 429. While the Token Bucket allows bursts up to bucket capacity, the Leaky Bucket **smooths out traffic spikes into a constant, uniform stream**.

```mermaid
flowchart TD
    subgraph Stage1 ["Stage 1: Traffic Ingress & Overflow Evaluation"]
        direction LR
        Burst["Bursty Ingress<br/>(Spikes of 500 req/s)"] --> Bucket["Leaky Bucket Buffer<br/>(Capacity: 50 slots)"] --> OverFlow{"Buffer Full?"}
        OverFlow -- Yes --> Drop["HTTP 429: Queue Overflow"]
    end

    subgraph Stage2 ["Stage 2: FIFO Regulation & Downstream Execution"]
        direction LR
        Queue["Enqueued in FIFO Buffer"] --> Leak["Constant Leak Rate<br/>(Smooth 10 req/s)"] --> BackendService["Downstream Engine"]
    end

    OverFlow -- No --> Queue

    style Stage1 fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Stage2 fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style Burst fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Bucket fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style OverFlow fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Drop fill:#7f1d1d,stroke:#ef4444,stroke-width:2px,color:#fff
    style Queue fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Leak fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style BackendService fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Code Example: Leaky Bucket Rate Limiter
```python
import time
import queue

class LeakyBucketLimiter:
    """
    Traffic shaping rate limiter enforcing smooth, constant-rate outbound request dispatch,
    buffering bursts up to capacity and rejecting excess requests with HTTP 429.
    """
    def __init__(self, capacity: int, leak_rate_per_sec: float):
        # Maximum allowed queued requests waiting for dispatch
        self.capacity = capacity
        # Smooth dispatch rate (requests per second allowed outbound)
        self.leak_rate = leak_rate_per_sec
        # Thread-safe FIFO queue representing the bucket buffer
        self.queue = queue.Queue(maxsize=capacity)
        # Epoch timestamp of previous leak evaluation
        self.last_leak_time = time.time()

    def leak(self) -> None:
        now = time.time()
        # Calculate elapsed duration since previous leak check
        elapsed = now - self.last_leak_time
        # Determine number of items allowed to drain based on elapsed time and leak rate
        leaks = int(elapsed * self.leak_rate)
        if leaks > 0:
            # Drain up to calculated quota from queue
            for _ in range(min(leaks, self.queue.qsize())):
                item = self.queue.get_nowait()
                print(f"  [LEAK] Dispatched request to downstream service: {item}")
            # Reset leak reference timestamp
            self.last_leak_time = now

    def submit_request(self, request_id: str) -> bool:
        # Drain completed slots first before evaluating buffer capacity
        self.leak()
        try:
            # Enqueue request non-blockingly into bounded FIFO buffer
            self.queue.put_nowait(request_id)
            print(f"[INGRESS] Enqueued: {request_id} (Queue occupancy: {self.queue.qsize()}/{self.capacity})")
            return True
        except queue.Full:
            # Buffer capacity exceeded: drop request and simulate HTTP 429 response
            print(f"[REJECTED] HTTP 429 Too Many Requests: {request_id} (Buffer Full)")
            return False

# Initialize limiter with capacity of 3 and smooth leak rate of 2 req/sec
limiter = LeakyBucketLimiter(capacity=3, leak_rate_per_sec=2.0)
# Submit burst of 5 concurrent requests: first 3 succeed, last 2 are rejected
for i in range(5):
    limiter.submit_request(f"req_{i}")
# Wait 600ms allowing 1 request to drain
time.sleep(0.6)
# Slot has opened: retry succeeds!
limiter.submit_request("req_retry")
```

#### References
- [GeeksforGeeks: Leaky Bucket Algorithm](https://www.geeksforgeeks.org/leaky-bucket-algorithm/)

---

### 5.3 Exponential Backoff with Jitter

```python
import time
import random
import requests
from typing import Dict, Any

def resilient_get(url: str, max_retries: int = 5) -> Dict[str, Any]:
    """
    Executes resilient HTTP GET request utilizing Exponential Backoff with Full Jitter
    to mitigate thundering herd problems against rate-limited or throttling APIs.
    """
    for attempt in range(max_retries):
        # Dispatch HTTP GET call
        resp = requests.get(url)
        # Success: Parse and return JSON payload directly
        if resp.status_code == 200:
            return resp.json()
        # HTTP 429 Too Many Requests: Server is throttling our client
        elif resp.status_code == 429:
            # Truncated exponential backoff: 2^0, 2^1, 2^2, capped at 60 seconds
            base_backoff = min(60.0, float(2 ** attempt))
            # Full Jitter: randomize delay uniformly between 0 and base_backoff to disperse concurrent client retries
            jitter_delay = random.uniform(0.1, base_backoff)
            print(f"[RETRY {attempt+1}/{max_retries}] Throttled (429). Sleeping {jitter_delay:.2f}s before retry...")
            time.sleep(jitter_delay)
        else:
            # Raise exception for terminal client/server errors (e.g. 401 Unauthorized, 404 Not Found)
            resp.raise_for_status()
    # Exhausted retry budget
    raise RuntimeError(f"API request to {url} failed after {max_retries} attempts")
```

---

# 6. Modern Integration Paradigms

---

### 6.1 Webhooks vs Long Polling

#### Architectural Comparison
- **Long Polling (Client Pull)**: The client initiates an HTTP request to the server, and the server holds the TCP socket open until new data arrives or a timeout occurs. Once the response returns, the client immediately establishes a new connection. Consumes substantial connection pool memory.
- **Webhooks (Server Push / Reverse API)**: The event producer makes an asynchronous HTTP `POST` request directly to the consumer's registered public URL the moment an event occurs. Near-zero resource consumption while idle; near-instantaneous event delivery.

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'actorBkg': '#1e293b', 'actorBorder': '#3b82f6', 'actorTextColor': '#ffffff', 'signalColor': '#93c5fd', 'signalTextColor': '#ffffff', 'noteBkgColor': '#1e293b', 'noteBorderColor': '#f59e0b', 'noteTextColor': '#fde68a', 'activationBkgColor': '#3b82f6', 'sequenceNumberColor': '#ffffff' }}}%%
sequenceDiagram
    autonumber
    actor Client
    participant Server

    Note over Client,Server: Long Polling (Pull Pattern)
    Client->>Server: GET /orders/updates (Holds TCP socket open)
    Note over Server: Server waits 25s until order is placed...
    Server-->>Client: 200 OK {order_id: 101}
    Client->>Server: GET /orders/updates (Immediately re-opens connection)

    Note over Client,Server: Webhook (Push Pattern)
    Note over Client,Server: Client registers webhook URL: https://data.corp.com/api/webhooks/orders
    Note over Server: Event occurs: Order 102 placed!
    Server->>Client: POST https://data.corp.com/api/webhooks/orders {order_id: 102}
    Client-->>Server: 200 OK (ACK)
```

#### References
- [GeeksforGeeks: What is Webhook?](https://www.geeksforgeeks.org/what-is-webhook/)
- [DataCamp: Webhooks vs APIs](https://www.datacamp.com/tutorial/webhooks-vs-apis)

---

### 6.2 Webhook Signature Verification

#### Defense against Replay & Spoofing Attacks
Secure webhooks include a cryptographic signature header (`X-Hub-Signature-256`). Receivers must re-compute the HMAC using their shared secret and verify equivalence:

```python
import hmac
import hashlib

def verify_webhook_signature(payload_bytes: bytes, received_signature: str, secret_key: str) -> bool:
    """
    Verifies cryptographic authenticity of inbound webhook payloads (e.g., Stripe, Shopify, GitHub),
    preventing man-in-the-middle tampering, spoofing, and timing attacks.
    """
    # Calculate HMAC-SHA256 digest using pre-shared secret key and raw payload bytes
    computed_digest = hmac.new(
        key=secret_key.encode("utf-8"),
        msg=payload_bytes,
        digestmod=hashlib.sha256
    ).hexdigest()
    # Format according to provider header standard (e.g. 'sha256=...')
    computed_signature = f"sha256={computed_digest}"
    # Use hmac.compare_digest for constant-time comparison to protect against timing attacks
    return hmac.compare_digest(computed_signature, received_signature)
```

---

### 6.3 Reverse ETL (Operational Analytics)

#### Definition
Traditional ETL pulls data from SaaS tools into the warehouse. **Reverse ETL** reverses the arrow: it reads curated, gold-layer customer models from the data warehouse (Snowflake, BigQuery, Databricks) and syncs them directly back into operational business applications (Salesforce CRM, Zendesk, Marketo, HubSpot).

```mermaid
flowchart LR
    DW[(Curated Data Warehouse: Snowflake / BigQuery)] -->|"Reverse ETL Engine (Census / Hightouch)"| OperationalApps
    
    subgraph OperationalApps ["Operational Business Applications"]
        SF[Salesforce: High-Value Customer LTV Flag]
        Zendesk[Zendesk: VIP Support Ticket Routing]
        Marketo[Marketo: Automated Churn Win-Back Campaign]
    end

    style DW fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style OperationalApps fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style SF fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Zendesk fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Marketo fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
```

---

### 6.4 Managed Data Integration Platforms (Fivetran vs Airbyte)

| Dimension | Fivetran | Airbyte |
| :--- | :--- | :--- |
| **Model** | Proprietary SaaS | **Open-Source (Self-Hosted) + Cloud SaaS** |
| **Connector Customization** | Closed-source connectors | **Open-Source Connector Development Kit (CDK)** |
| **Pricing Metric** | Monthly Active Rows (MAR) | Compute / Capacity (Cloud) or Free (Self-Hosted) |
| **Enterprise Support** | Turnkey zero-maintenance enterprise standard | Highly customizable for internal private APIs |

#### References
- [Fivetran Documentation](https://fivetran.com/docs)
- [Airbyte Official Documentation](https://docs.airbyte.com/)
- [DataCamp: Reverse ETL and Modern Data Integration](https://www.datacamp.com/tutorial/reverse-etl)

---

---

# 7. References & Further Reading

- [Official Documentation & Design Principles Guide](https://docs.microsoft.com/en-us/azure/architecture/)
- [High Performance Distributed Data Architecture Patterns](https://martinfowler.com/articles/patterns-of-distributed-systems/)
- [Designing Data-Intensive Applications (Martin Kleppmann)](https://dataintensive.net/)
