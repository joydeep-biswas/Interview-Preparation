# 12. Troubleshooting, JVM Diagnostics, Performance Tuning & Production Scenarios

A comprehensive production troubleshooting guide answering all 17 questions on JDWP remote debugging, high CPU vs low CPU bottlenecks, thread contention, rare deadlocks, off-heap memory leaks, GC pause trade-offs, retry storms, Spring bean failures, and 503/504 gateway diagnosis.

---

## Architectural Overview: Production Troubleshooting Decision Tree

```mermaid
flowchart TD
    Symptom["Production Incident: Service Degraded / Slow"] --> MetricCheck{"Inspect CPU Utilization"}

    MetricCheck -->|CPU is LOW (< 20%)| LowCPU["Investigate Thread Blocking / I/O Wait"]
    LowCPU --> CheckPools["Check DB Hikari Pool, Thread Dumps (jstack) for BLOCKED/WAITING"]
    
    MetricCheck -->|CPU is HIGH (> 90%)| HighCPU["Investigate CPU Consumers"]
    HighCPU --> ThreadProfiling["Run 'top -H -p PID' + 'printf %x TID' -> Match jstack nid"]
    ThreadProfiling --> RootCauseHigh{"High CPU Source"}
    RootCauseHigh -->|JVM GC Threads (VM Thread)| GCThrash["GC Thrashing / Heap Exhaustion"]
    RootCauseHigh -->|App Thread| InfiniteLoop["Infinite Loop / Regex ReDoS / Spin-Wait"]

    Symptom --> NetworkErr{"HTTP Status Code"}
    NetworkErr -->|504 Gateway Timeout| DownstreamSlow["Upstream Gateway timed out waiting for backend"]
    NetworkErr -->|503 Service Unavailable| BackendExhaust["Tomcat thread pool full / K8s pod crash / ingress failure"]

    style Symptom fill:#0f172a,stroke:#ef4444,stroke-width:2px,color:#fca5a5
    style MetricCheck fill:#1e293b,stroke:#8b5cf6,stroke-width:1.5px,color:#c4b5fd
    style LowCPU fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style HighCPU fill:#0f172a,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style CheckPools fill:#1f2937,stroke:#3b82f6,stroke-width:1px,color:#fff
    style ThreadProfiling fill:#1f2937,stroke:#f59e0b,stroke-width:1px,color:#fff
```

---

## Table of Contents & Question Mapping

1. **Java Debugging & Agent Architecture**
   - [Q1 & Q2. Local and Remote Debugging via JDWP & Port Distinctions](#q1--q2-local-and-remote-debugging-via-jdwp--port-distinctions)
2. **CPU & Latency Production Anomalies**
   - [Q3. Service Slow but CPU Usage is Low: What to Check First](#q3-service-slow-but-cpu-usage-is-low-what-to-check-first)
   - [Q4. Application Runs Fine for Hours, Then Suddenly Slows Down](#q4-application-runs-fine-for-hours-then-suddenly-slows-down)
   - [Q5. High CPU Usage with Very Low Traffic: Investigation Steps](#q5-high-cpu-usage-with-very-low-traffic-investigation-steps)
   - [Q6. Inconsistent API Response Time Without Code Changes](#q6-inconsistent-api-response-time-without-code-changes)
   - [Q7. Isolating Background Jobs from Impacting API Latency](#q7-isolating-background-jobs-from-impacting-api-latency)
   - [Q8. Debugging Rare Production Deadlocks that Cannot be Reproduced Locally](#q8-debugging-rare-production-deadlocks-that-cannot-be-reproduced-locally)
3. **Memory, Garbage Collection & Collections Degradation**
   - [Q9. Memory Usage Keeps Increasing with No Obvious Heap Leaks](#q9-memory-usage-keeps-increasing-with-no-obvious-heap-leaks)
   - [Q10. `HashMap` Lookup Time Degrades from $O(1)$ to $O(N)$](#q10-hashmap-lookup-time-degrades-from-o1-to-on)
   - [Q11. GC Pauses Increase Significantly After a Small Release](#q11-gc-pauses-increase-significantly-after-a-small-release)
   - [Q12. JVM Tuning: Throughput vs Latency Trade-Off](#q12-jvm-tuning-throughput-vs-latency-trade-off)
4. **Distributed Cascades, Scaling & Logging Pitfalls**
   - [Q13. Why Increasing Log Verbosity Degrades Performance](#q13-why-increasing-log-verbosity-degrades-performance)
   - [Q14. Why Scaling Instances Worsens Overall System Performance](#q14-why-scaling-instances-worsens-overall-system-performance)
   - [Q15. Retry Storms: How Poorly Configured Retries Cause Cascading Outages](#q15-retry-storms-how-poorly-configured-retries-cause-cascading-outages)
5. **Spring & Gateway Failures**
   - [Q16. Troubleshooting `NoSuchBeanDefinitionException` in Spring Boot](#q16-troubleshooting-nosuchbeandefinitionexception-in-spring-boot)
   - [Q17. Troubleshooting and Resolving 504 Gateway Timeout and 503 Service Unavailable](#q17-troubleshooting-and-resolving-504-gateway-timeout-and-503-service-unavailable)

---

# 1. Java Debugging & Agent Architecture

---

### Q1 & Q2. Local and Remote Debugging via JDWP & Port Distinctions
*(Grouped together: Java Remote Debugging Mechanics)*  
**Topic Covered:** JVM Tool Interface (JVMTI) & JDWP Protocol  
Attach the JDWP agent on remote launch:
```bash
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005 -jar app.jar
```
- **Application Server Port (e.g. 8080):** Serves customer HTTP traffic.
- **JVM Remote Debug Port (e.g. 5005):** Binary protocol exclusively for IDE breakpoint introspection. Must never be exposed publicly.

---

# 2. CPU & Latency Production Anomalies

---

### Q3. Service Slow but CPU Usage is Low: What to Check First
**Topic Covered:** Thread Blocking & Bottleneck Identification  
Threads are in `WAITING` or `BLOCKED` states waiting for external resources.
1. **HikariCP Pool Exhaustion:** Database connection pool starvation (`Connection is not available, request timed out after 30000ms`).
2. **Missing I/O Timeouts:** Outbound HTTP/gRPC calls blocking without socket read timeouts.
3. **Lock Contention:** Many threads waiting on a single synchronized lock or database row lock.

---

### Q4. Application Runs Fine for Hours, Then Suddenly Slows Down
**Topic Covered:** Progressive Degradation Diagnostics  
- Memory leak exhausting heap, forcing continuous Full GC thrashing.
- Unclosed database connections or file descriptors leaking over time.
- Database table grew past RAM buffer pool, forcing sequential disk scans.

---

### Q5. High CPU Usage with Very Low Traffic: Investigation Steps
**Topic Covered:** Thread Profiling & Hotspot Tracking  
1. Find top thread ID: `top -H -p <PID>`.
2. Convert thread ID to hex: `printf "%x\n" <TID>`.
3. Capture thread dump and grep: `jstack <PID> | grep -A 30 "0x<hex_id>"`.
4. Inspect code for infinite loops, regex catastrophic backtracking (ReDoS), or JVM `VM Thread` spinning on Full GC.

---

### Q6. Inconsistent API Response Time Without Code Changes
**Topic Covered:** Latency Jitter & JVM Warmup  
- Periodic Stop-The-World (STW) Garbage Collection pauses.
- JVM Warmup (Tiered JIT compilation hasn't optimized hot paths yet).
- Noisy neighbor problem in shared cloud VM/container nodes.

---

### Q7. Isolating Background Jobs from Impacting API Latency
**Topic Covered:** Workload Resource Isolation  
- Assign background jobs to a dedicated bounded `ThreadPoolExecutor` (never use `ForkJoinPool.commonPool()` or web server thread pools).
- Deploy heavy batch tasks to separate worker pods.
- Route reporting queries to dedicated database read replicas.

---

### Q8. Debugging Rare Production Deadlocks that Cannot be Reproduced Locally
**Topic Covered:** Post-Mortem Concurrency Debugging  
Capture thread dump via `jcmd <PID> Thread.print` or `kill -3 <PID>`. The JVM automatically outputs cyclic monitor dependency sections (`Found one Java-level deadlock`). Enforce global lock ordering to permanently resolve.

---

# 3. Memory, Garbage Collection & Collections Degradation

---

### Q9. Memory Usage Keeps Increasing with No Obvious Heap Leaks
**Topic Covered:** Off-Heap & Native Memory Analysis  
If heap usage is stable but process memory (RSS) climbs:
- Metaspace class generation leak (CGLIB/ByteBuddy).
- Direct ByteBuffers allocated off-heap by Netty or Java NIO.
- Thread stack memory (each thread takes 1MB native stack via `-Xss`).
- Diagnose using Native Memory Tracking: `-XX:NativeMemoryTracking=summary`.

---

### Q10. `HashMap` Lookup Time Degrades from $O(1)$ to $O(N)$
**Topic Covered:** Hash Distribution Skew  
Poorly implemented `hashCode()` returning identical hash values lands all entries in a single bucket. If keys do not implement `Comparable`, treeification fails to maintain an efficient tree and falls back to identity comparisons.

---

### Q11. GC Pauses Increase Significantly After a Small Release
**Topic Covered:** GC Allocation Rate & Tenuring  
High allocation rate of short-lived large objects bypassing young generation survivor spaces and prematurely promoting to the Old Generation. Inspect using GC logs (`-Xlog:gc*`).

---

### Q12. JVM Tuning: Throughput vs Latency Trade-Off
**Topic Covered:** GC Collector Mechanics  
- **Throughput-Oriented (Parallel GC / G1GC):** Maximizes application CPU time; accepts longer STW pauses.
- **Low-Latency (ZGC / Shenandoah in Java 21):** Concurrent marking and compaction with sub-millisecond pauses; trades ~10-15% peak throughput overhead.

---

# 4. Distributed Cascades, Scaling & Logging Pitfalls

---

### Q13. Why Increasing Log Verbosity Degrades Performance
**Topic Covered:** I/O Bottlenecks & Synchronous Logging  
Synchronous disk I/O blocks worker threads on every log write flush. String concatenations in debug logs create millions of short-lived heap objects. Use **Asynchronous Appenders** with LMAX Disruptor ring buffers.

---

### Q14. Why Scaling Instances Worsens Overall System Performance
**Topic Covered:** Database Connection Pool Saturation  
Scaling from 5 to 20 instances increases total database connections from 150 to 600. The database server spends all CPU context-switching and managing lock contention, causing query latencies to spike and the system to collapse. Use a connection pooler like **PgBouncer**.

---

### Q15. Retry Storms: How Poorly Configured Retries Cause Cascading Outages
**Topic Covered:** Thundering Herd & Cascading Failure  
When a service lags, uncoordinated client retries amplify load $3\times-5\times$, causing total failure. Prevent with **Exponential Backoff**, **Full Jitter**, and **Circuit Breakers**.

---

# 5. Spring & Gateway Failures

---

### Q16. Troubleshooting `NoSuchBeanDefinitionException` in Spring Boot
**Topic Covered:** Spring IoC Diagnostics  
1. Class is outside package scanned by `@SpringBootApplication`.
2. Missing stereotype annotation (`@Component`, `@Service`).
3. Bean suppressed by `@ConditionalOnProperty` or inactive `@Profile`.

---

### Q17. Troubleshooting and Resolving 504 Gateway Timeout and 503 Service Unavailable
**Topic Covered:** HTTP Edge Diagnostics  
- **504 Gateway Timeout:** The gateway timed out waiting for the Spring Boot backend service. Caused by slow database queries, deadlocks, or long-running computations.
- **503 Service Unavailable:** The gateway cannot connect to any healthy backend instance. Caused by pod crashes (`CrashLoopBackOff`), failed readiness probes, or saturated Tomcat connection queues.
