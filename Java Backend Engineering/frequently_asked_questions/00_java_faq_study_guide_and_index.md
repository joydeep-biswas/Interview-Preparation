# Java Backend Engineering Interview Questions & Comprehensive Solutions Master Guide

This directory contains the production-grade, deeply analyzed answers to the complete collection of real-world Java Backend Engineering interview scenarios, live coding implementations, and enterprise architecture designs.

All questions have been systematically grouped by conceptual similarity, mapped to enterprise Java backend disciplines, and organized in the **optimal chronological order in which they should be studied**. Each guide provides formal definitions, architectural diagrams, mathematical/memory analysis, production-grade code snippets, edge-case handling, and troubleshooting playbooks matching the rigorous style of the base concepts guides.

---

## Recommended Chronological Study Order

```mermaid
flowchart TD
    subgraph Tier1 ["Tier 1: Core Java Language, Data Structures & Concurrency"]
        direction LR
        Step1["01. Core Java & Algorithms<br/>(Memory, two-pointers, virtual threads)"] --> Step2["02. Collections Framework<br/>(HashMap, ConcurrentHashMap CAS)"] --> Step3["03. Modern Java & Streams<br/>(Lambdas, Streams, Records)"] --> Step4["04. Concurrency & Async<br/>(JMM, volatile, ThreadPools, deadlocks)"]
    end

    subgraph Tier2 ["Tier 2: Spring Framework, REST, Security & Databases"]
        direction LR
        Step5["05. Spring Boot & REST APIs<br/>(IoC container, Auto-config, REST)"] --> Step6["06. Security, OAuth2 & JWT<br/>(SecurityFilterChain, BCrypt, PKCE)"] --> Step7["07. Databases, JPA & Hibernate<br/>(ACID, N+1 query, @Transactional)"]
    end

    subgraph Tier3 ["Tier 3: Distributed Systems, Cloud Containers & Reliability"]
        direction LR
        Step8["08. Messaging: Kafka & RMQ<br/>(Partitions, consumer groups, DLQ)"] --> Step9["09. Microservices Design<br/>(Circuit Breakers, Saga, Outbox, CAP)"] --> Step10["10. Cloud, DevOps & Troubleshooting<br/>(Docker, K8s, Thread dumps, GC tuning)"]
    end

    Step4 --> Step5
    Step7 --> Step8

    style Tier1 fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Tier2 fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c4b5fd
    style Tier3 fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7

    style Step1 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Step2 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Step3 fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Step4 fill:#1f2937,stroke:#06b6d4,stroke-width:2px,color:#fff
    style Step5 fill:#1f2937,stroke:#06b6d4,stroke-width:2px,color:#fff
    style Step6 fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style Step7 fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style Step8 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Step9 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Step10 fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
```

---

## Topic Guides & Coverage Overview

| Study Order | Guide File | Questions Covered | Corresponding Base Concepts | Key Topics & Grouped Scenarios |
| :---: | :--- | :---: | :--- | :--- |
| **01** | [`01_core_java_and_oop_faq.md`](01_core_java_and_oop_faq.md) | Core Java & OOP<br/>(Q1–Q38) | [`01_core_java_and_oop.md`](../01_core_java_and_oop.md) | • **OOP & Contracts:** String immutability, equals/hashCode contract, shallow vs deep copy.<br/>• **Algorithms & Data Handling:** Palindrome validation, anagram detection, stack-based parentheses.<br/>• **Production Design:** Thread-safe singleton, deep serialization, defensive immutability. |
| **02** | [`02_java_collections_and_generics_faq.md`](02_java_collections_and_generics_faq.md) | Collections Framework<br/>(Q1–Q30) | [`02_java_collections_and_generics.md`](../02_java_collections_and_generics.md) | • **Hierarchy & Implementations:** List vs Set, ArrayList vs LinkedList, ArrayDeque vs Stack.<br/>• **Hashing Mechanics:** HashMap bucket collision, Red-Black treeification, load factor dynamics.<br/>• **Concurrency & Generics:** ConcurrentHashMap segment/CAS locking, CopyOnWriteArrayList, PECS. |
| **03** | [`03_modern_java_streams_and_records_faq.md`](03_modern_java_streams_and_records_faq.md) | Modern Java & Streams<br/>(Q1–Q12) | [`03_modern_java_streams_and_records.md`](../03_modern_java_streams_and_records.md) | • **Functional Foundations:** @FunctionalInterface, built-in SAM types, method reference syntax.<br/>• **Stream API Pipelines:** Intermediate vs terminal ops, loop fusion, map vs flatMap, short-circuiting.<br/>• **Language Evolution:** Optional best practices, Java 16 Records, Java 17 Sealed hierarchies. |
| **04** | [`04_java_concurrency_and_multithreading_faq.md`](04_java_concurrency_and_multithreading_faq.md) | Multithreading & JMM<br/>(Q1–Q15) | [`04_java_concurrency_and_multithreading.md`](../04_java_concurrency_and_multithreading.md) | • **Concurrency Foundations:** Runnable vs Callable, Thread lifecycle, wait/notify monitor mechanics.<br/>• **JMM Internals:** Volatile visibility guarantees, happens-before edges, Atomic CAS operations.<br/>• **Executors & Loom:** ThreadPoolExecutor queuing policies, CompletableFuture async, Virtual Threads. |
| **05** | [`05_spring_boot_and_rest_apis_faq.md`](05_spring_boot_and_rest_apis_faq.md) | Spring Boot & REST APIs<br/>(Q1–Q37) | [`05_spring_boot_and_rest_apis.md`](../05_spring_boot_and_rest_apis.md) | • **IoC & Bean Lifecycle:** BeanFactory vs ApplicationContext, Constructor injection, auto-configuration.<br/>• **MVC Dispatcher Pipeline:** HandlerMapping, HandlerAdapter, Interceptors, MessageConverters.<br/>• **Enterprise API Design:** REST statelessness, PUT vs PATCH, ProblemDetail error format, idempotency keys. |
| **06** | [`06_security_and_cryptography_faq.md`](06_security_and_cryptography_faq.md) | Security, OAuth2 & JWT<br/>(Q1–Q17) | [`06_security_and_cryptography.md`](../06_security_and_cryptography.md) | • **Security Architecture:** SecurityFilterChain ordering, AuthenticationManager, custom providers.<br/>• **Authentication Tokens:** Stateless JWT RS256 validation, refresh token rotation, HttpOnly cookies.<br/>• **OAuth2 & Permissions:** OAuth2 Authorization Code + PKCE, method security SpEL expressions. |
| **07** | [`07_databases_jpa_and_hibernate_faq.md`](07_databases_jpa_and_hibernate_faq.md) | Databases, JPA & Hibernate<br/>(Q1–Q25) | [`07_databases_jpa_and_hibernate.md`](../07_databases_jpa_and_hibernate.md) | • **Relational Foundations:** ACID transaction guarantees, ANSI isolation levels, B-Tree index scans.<br/>• **ORM Internals:** Entity lifecycle states, First-Level vs Second-Level cache, dirty checking engine.<br/>• **Performance & Triage:** N+1 query problem resolution (JOIN FETCH, EntityGraph), locking strategies. |
| **08** | [`08_caching_redis_and_kafka_faq.md`](08_caching_redis_and_kafka_faq.md) | Messaging: Kafka & RabbitMQ<br/>(Q1–Q11) | [`08_caching_redis_and_kafka.md`](../08_caching_redis_and_kafka.md) | • **Broker Architecture:** RabbitMQ AMQP exchanges/bindings vs Kafka partitioned commit log.<br/>• **Message Delivery Invariants:** At-least-once, idempotent consumer design, consumer group rebalances.<br/>• **Resilience:** Dead Letter Queues, retry backoff with jitter, out-of-order event handling. |
| **09** | [`09_microservices_and_system_design_faq.md`](09_microservices_and_system_design_faq.md) | Microservices & Architecture<br/>(Q1–Q25) | [`09_microservices_and_system_design.md`](../09_microservices_and_system_design.md) | • **Distributed Foundations:** Monolith vs Microservices, DDD Bounded Contexts, CAP/PACELC trade-offs.<br/>• **Communication & Resilience:** API Gateway routing, Resilience4j Circuit Breakers, Bulkheads.<br/>• **Distributed Data Patterns:** Saga Orchestration, Transactional Outbox pattern, CQRS architecture. |
| **10** | [`10_cloud_devops_and_observability_faq.md`](10_cloud_devops_and_observability_faq.md) | Cloud, DevOps & Troubleshooting<br/>(Q1–Q44) | [`10_cloud_devops_and_observability.md`](../10_cloud_devops_and_observability.md) | • **Containerization & Cloud:** Multi-stage Docker builds, Kubernetes probes, autoscaling, cloud identity.<br/>• **Memory Diagnostics:** OutOfMemoryError root cause analysis, heap dumps, GC pause optimization.<br/>• **Production Resilience:** Connection pool exhaustion, cascading timeouts, retry storms mitigation. |

---

## Master Topic Directory & Difficulty Matrix

| Category | Domain & Focus Area | Questions | Progressive Difficulty Distribution | Companion Deep-Dive Guide |
| :---: | :--- | :---: | :--- | :--- |
| **01** | [Core Java, Object-Oriented Programming & Algorithms](#01--core-java-object-oriented-programming--algorithms) | 38 Questions | Easy: 13 | Medium: 28 | Hard: 4 | Expert: 1 | [`01_core_java_oop_and_`<br/>`algorithms_faq.md`](01_core_java_and_oop_faq.md) |
| **02** | [Java Collections Framework, Generics & Data Handling](#02--java-collections-framework-generics--data-handling) | 30 Questions | Easy: 9 | Medium: 26 | Hard: 4 | Expert: 0 | [`02_java_collections_and_generics_faq.md`](02_java_collections_and_generics_faq.md) |
| **03** | [Modern Java Features, Lambdas & Functional Streams](#03--modern-java-features-lambdas--functional-streams) | 12 Questions | Easy: 6 | Medium: 10 | Hard: 1 | Expert: 0 | [`03_modern_java_streams_and_`<br/>`lambdas_faq.md`](03_modern_java_streams_and_records_faq.md) |
| **04** | [Multithreading, Concurrency & Asynchronous Programming](#04--multithreading-concurrency--asynchronous-programming) | 15 Questions | Easy: 3 | Medium: 11 | Hard: 7 | Expert: 1 | [`04_java_concurrency_`<br/>`and_multithreading_faq.md`](04_java_concurrency_and_multithreading_faq.md) |
| **05** | [Spring Framework, Spring Boot & REST APIs](#05--spring-framework-spring-boot--rest-apis) | 37 Questions | Easy: 15 | Medium: 19 | Hard: 8 | Expert: 3 | [`05_spring_boot_and_`<br/>`rest_apis_faq.md`](05_spring_boot_and_rest_apis_faq.md) |
| **06** | [Spring Security, OAuth2, OpenID Connect & JWT Architecture](#06--spring-security-oauth2-openid-connect--jwt-architecture) | 17 Questions | Easy: 5 | Medium: 10 | Hard: 4 | Expert: 3 | [`06_spring_security_and_`<br/>`oauth2_jwt_faq.md`](06_security_and_cryptography_faq.md) |
| **07** | [Relational Databases, SQL Optimization, JPA & Hibernate ORM](#07--relational-databases-sql-optimization-jpa--hibernate-orm) | 25 Questions | Easy: 7 | Medium: 17 | Hard: 5 | Expert: 1 | [`07_databases_jpa_and_hibernate_faq.md`](07_databases_jpa_and_hibernate_faq.md) |
| **08** | [Distributed Messaging Systems: Apache Kafka & RabbitMQ](#08--distributed-messaging-systems-apache-kafka--rabbitmq) | 11 Questions | Easy: 2 | Medium: 4 | Hard: 6 | Expert: 1 | [`08_messaging_systems_kafka_`<br/>`rabbitmq_faq.md`](08_caching_redis_and_kafka_faq.md) |
| **09** | [Microservices Architecture, Distributed Systems & System Design](#09--microservices-architecture-distributed-systems--system-design) | 25 Questions | Easy: 3 | Medium: 12 | Hard: 9 | Expert: 11 | [`09_microservices_and_`<br/>`system_design_faq.md`](09_microservices_and_system_design_faq.md) |
| **10** | [Cloud Native, DevOps, Observability & Production Troubleshooting](#10--cloud-native-devops-observability--production-troubleshooting) | 44 Questions | Easy: 9 | Medium: 22 | Hard: 14 | Expert: 3 | [`10_cloud_devops_observability_and_`<br/>`troubleshooting_faq.md`](10_cloud_devops_and_observability_faq.md) |
| **TOTAL** | **Enterprise Java Backend Preparation Bank** | **317 Questions** | **All Modules Fully Covered** | **[10 Companion Guides](#topic-guides--coverage-overview)** |

---

## 01 – Core Java, Object-Oriented Programming & Algorithms
*Companion Deep-Dive Guide: [`01_core_java_and_oop_faq.md`](01_core_java_and_oop_faq.md) (38 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between the == operator and the equals() method in Java? → [Solution](01_core_java_and_oop_faq.md#q11--vs-equals-and-the-string-pool-mechanism)
2. **`[Easy]`** How does short-circuiting work with logical operators (&&, ||) in Java? → [Solution](01_core_java_and_oop_faq.md#q12-short-circuiting-with-logical-operators--)
3. **`[Easy]`** What is the use of constructor in Java? → [Solution](01_core_java_and_oop_faq.md#q13-constructors-and-invoking-static-methods-inside-constructors)
4. **`[Easy]`** Can you invoke a static method inside a constructor in Java? → [Solution](01_core_java_and_oop_faq.md#q13-constructors-and-invoking-static-methods-inside-constructors)
5. **`[Easy]`** What is the purpose of the finally block in Java exception handling? → [Solution](01_core_java_and_oop_faq.md#q14-final-finally-and-finalize)
6. **`[Easy]`** What are the differences between final, finally, and finalize() in Java? → [Solution](01_core_java_and_oop_faq.md#q14-final-finally-and-finalize)
7. **`[Easy]`** What is the difference between StringBuffer and StringBuilder? → [Solution](01_core_java_and_oop_faq.md#q23-stringbuffer-vs-stringbuilder)
8. **`[Easy]`** Contrast Method Overloading vs Method Overriding, and predict the output of an inheritance snippet involving static methods (Method Hiding vs Dynamic Dispatch). → [Solution](01_core_java_and_oop_faq.md#q34-static-method-hiding-vs-dynamic-method-overriding-in-java-inheritance)
9. **`[Easy]`** What is method hiding in Java and how does it differ from overriding? → [Solution](01_core_java_and_oop_faq.md#q19-overriding-static-methods--method-hiding)
10. **`[Easy]`** Can you override a static method in Java? → [Solution](01_core_java_and_oop_faq.md#q19-overriding-static-methods--method-hiding)
11. **`[Easy]`** What is an anonymous inner class, and how is it used in Java? → [Solution](01_core_java_and_oop_faq.md#q20-anonymous-inner-classes--scoping)
12. **`[Easy]`** What is a marker interface, and what are its practical use cases? → [Solution](01_core_java_and_oop_faq.md#q18-marker-interfaces-and-modern-alternatives)
13. **`[Easy]`** What are text blocks, and where are they useful in modern Java? → [Solution](01_core_java_and_oop_faq.md#q29-text-blocks-and-multiline-strings)
14. **`[Medium]`** How do you reverse a string in Java without using built-in API functions? → [Solution](01_core_java_and_oop_faq.md#q1-reverse-a-string-without-built-ins)
15. **`[Medium]`** How do you convert the first half of a string to lowercase and the second half to uppercase in Java? → [Solution](01_core_java_and_oop_faq.md#q2-convert-first-half-to-lowercase--second-half-to-uppercase)
16. **`[Medium]`** How do you find the first non-repeating character in a string? → [Solution](01_core_java_and_oop_faq.md#q3-first-non-repeating-character-in-a-string)
17. **`[Medium]`** How do you find the length of the longest substring without repeating characters? → [Solution](01_core_java_and_oop_faq.md#q4-longest-substring-without-repeating-characters)
18. **`[Medium]`** How do you find the longest common prefix among an array of strings? → [Solution](01_core_java_and_oop_faq.md#q5-longest-common-prefix-in-an-array-of-strings)
19. **`[Medium]`** How do you remove duplicates from an array in a single iteration? → [Solution](01_core_java_and_oop_faq.md#q6-remove-duplicates-from-an-array-in-a-single-iteration)
20. **`[Medium]`** How do you rearrange an integer array so that all even numbers appear before all odd numbers? → [Solution](01_core_java_and_oop_faq.md#q7-rearrange-integer-array-even-before-odd)
21. **`[Medium]`** How do you print prime numbers between 1 and 200 with minimal time complexity (Sieve of Eratosthenes)? → [Solution](01_core_java_and_oop_faq.md#q8-print-prime-numbers-between-1-and-200-minimal-complexity)
22. **`[Medium]`** How do you reverse a singly linked list iteratively and recursively? → [Solution](01_core_java_and_oop_faq.md#q9-reverse-a-singly-linked-list-iterative--recursive)
23. **`[Medium]`** Detect a cycle in a singly linked list without using extra space (Floyd's Tortoise and Hare Algorithm). → [Solution](01_core_java_and_oop_faq.md#q33-detect-a-cycle-in-a-singly-linked-list-without-using-extra-space)
24. **`[Medium]`** How do you verify if a path from root to leaf in a binary tree sums to a given target value? → [Solution](01_core_java_and_oop_faq.md#q10-path-sum-verification-in-a-binary-tree-root-to-leaf)
25. **`[Medium]`** Given String s1 = '00011111', check whether zeros or ones occur more frequently without using a third variable. → [Solution](01_core_java_and_oop_faq.md#q35-character-parity--zero-vs-one-frequency-comparison-without-using-a-third-variable)
26. **`[Medium]`** Given rows of student name and score, return the highest average score, rounded down. → [Solution](01_core_java_and_oop_faq.md#q36-highest-average-student-score-rounded-down-from-tabular-data)
27. **`[Medium]`** What are checked exceptions vs unchecked exceptions? What are their advantages, disadvantages, and performance impact? → [Solution](01_core_java_and_oop_faq.md#q37-checked-vs-unchecked-exceptions-performance-overhead--controlleradvice-exception-handling)
28. **`[Medium]`** Why is composition preferred over inheritance in object-oriented design? → [Solution](01_core_java_and_oop_faq.md#q16-composition-over-inheritance)
29. **`[Medium]`** When should you use an interface versus an abstract class in Java? → [Solution](01_core_java_and_oop_faq.md#q17-interface-vs-abstract-class--static-vs-default-methods)
30. **`[Medium]`** What is the difference between static and default methods in Java interfaces? → [Solution](01_core_java_and_oop_faq.md#q17-interface-vs-abstract-class--static-vs-default-methods)
31. **`[Medium]`** How do you create a deeply immutable class in Java? → [Solution](01_core_java_and_oop_faq.md#q21-designing-completely-immutable-classes)
32. **`[Medium]`** What is the difference between shallow copy and deep copy in Java? → [Solution](01_core_java_and_oop_faq.md#q22-shallow-copy-vs-deep-copy)
33. **`[Medium]`** How does the String Pool work in Java, and why are string literals pooled? → [Solution](01_core_java_and_oop_faq.md#q11--vs-equals-and-the-string-pool-mechanism)
34. **`[Medium]`** What causes a ClassCastException, and how can it be prevented defensively? → [Solution](01_core_java_and_oop_faq.md#q15-classcastexception-root-causes-and-prevention)
35. **`[Medium]`** What are the different types of class loaders in Java (Bootstrap, Platform/Extension, Application)? → [Solution](01_core_java_and_oop_faq.md#q25-classloader-architecture-and-delegation-model)
36. **`[Medium]`** What is the Java Reflection API, and what are its practical use cases and performance costs? → [Solution](01_core_java_and_oop_faq.md#q26-java-reflection-api--dynamic-proxies)
37. **`[Medium]`** What is the difference between the Path and File classes in Java NIO vs IO? → [Solution](01_core_java_and_oop_faq.md#q27-java-nio-path-vs-classic-io-file)
38. **`[Medium]`** How does pattern matching for switch work in modern Java (Java 17 / 21)? → [Solution](01_core_java_and_oop_faq.md#q30-pattern-matching-for-switch)
39. **`[Medium]`** What are the differences between records, standard classes, and sealed classes in modern Java? → [Solution](01_core_java_and_oop_faq.md#q31-records-standard-classes--sealed-classes-in-production)
40. **`[Medium]`** Can you explain a scenario where sealed classes were beneficial in enforcing stricter type hierarchies in your project? → [Solution](01_core_java_and_oop_faq.md#q31-records-standard-classes--sealed-classes-in-production)
41. **`[Hard]`** Explain memory management in Java (Heap, Stack, Metaspace, GC and Object Headers). → [Solution](01_core_java_and_oop_faq.md#q24-jvm-memory-architecture--handling-outofmemoryerror)
42. **`[Hard]`** What are some common causes of OutOfMemoryError (Heap, Metaspace, DirectBuffer) in Java and how do you handle them? → [Solution](01_core_java_and_oop_faq.md#q24-jvm-memory-architecture--handling-outofmemoryerror)
43. **`[Hard]`** What is a Proxy class in Java and how is it used for dynamic method invocation and AOP interception? → [Solution](01_core_java_and_oop_faq.md#q26-java-reflection-api--dynamic-proxies)
44. **`[Hard]`** How does Java handle tail call optimization (TCO), and why does HotSpot intentionally preserve complete stack frames? → [Solution](01_core_java_and_oop_faq.md#q28-tail-call-optimization-in-java)
45. **`[Expert]`** What are virtual threads in Java 21, and how do they differ from platform threads under carrier thread unmounting? → [Solution](01_core_java_and_oop_faq.md#q32-virtual-threads-project-loom-vs-platform-threads)
46. **`[Medium]`** Given a string of opening and closing parentheses/brackets, write an efficient program to validate if the brackets are balanced. Compare stack vs recursion. → [Solution](01_core_java_and_oop_faq.md#q38-validating-balanced-parentheses--brackets-stack-based-vs-recursive-stackless-approaches)

---

## 02 – Java Collections Framework, Generics & Data Handling

*Companion Deep-Dive Guide: [`02_java_collections_and_generics_faq.md`](02_java_collections_and_generics_faq.md) (30 Questions & Production Solutions)*

1. **`[Easy]`** What is the main difference between List and Set in Java Collections? → [Solution](02_java_collections_and_generics_faq.md#q1-list-vs-set-interfaces)
2. **`[Easy]`** What are the key differences between ArrayList and LinkedList? → [Solution](02_java_collections_and_generics_faq.md#q2-arraylist-vs-linkedlist)
3. **`[Easy]`** What is the difference between Arrays.asList() and new ArrayList<>()? → [Solution](02_java_collections_and_generics_faq.md#q3-arraysaslist-vs-listof-vs-new-arraylist)
4. **`[Easy]`** What are the differences between List.of() and new ArrayList<>() introduced in Java 9+? → [Solution](02_java_collections_and_generics_faq.md#q3-arraysaslist-vs-listof-vs-new-arraylist)
5. **`[Easy]`** How do HashSet, LinkedHashSet, and TreeSet differ in terms of ordering and performance? → [Solution](02_java_collections_and_generics_faq.md#q4-hashset-vs-linkedhashset-vs-treeset)
6. **`[Easy]`** Can a HashMap store null keys, and at which bucket index are null keys stored? → [Solution](02_java_collections_and_generics_faq.md#q7-hashmap-internals-capacity-load-factor-resizing-null-keys--collisions)
7. **`[Easy]`** What are the differences between poll(), peek(), and remove() methods in a Queue? → [Solution](02_java_collections_and_generics_faq.md#q14-poll-vs-peek-vs-remove)
8. **`[Easy]`** What are the differences between Iterator and ListIterator in Java? → [Solution](02_java_collections_and_generics_faq.md#q15-iterator-vs-listiterator)
9. **`[Easy]`** What is the difference between Comparable and Comparator interfaces? → [Solution](02_java_collections_and_generics_faq.md#q17-comparable-vs-comparator)
10. **`[Medium]`** How does a HashSet work internally, and how does it ensure unique elements? → [Solution](02_java_collections_and_generics_faq.md#q5-hashset-internal-working--uniqueness-guarantee)
11. **`[Medium]`** What is the contract between hashCode() and equals(), and what happens if it is violated? → [Solution](02_java_collections_and_generics_faq.md#q6-the-hashcode-and-equals-contract--custom-keys)
12. **`[Medium]`** Which methods must you override when using a custom object as a Map key? → [Solution](02_java_collections_and_generics_faq.md#q6-the-hashcode-and-equals-contract--custom-keys)
13. **`[Medium]`** What are the default initial capacity, load factor, and threshold of a HashMap? → [Solution](02_java_collections_and_generics_faq.md#q7-hashmap-internals-capacity-load-factor-resizing-null-keys--collisions)
14. **`[Medium]`** What happens when two keys collide in a HashMap (LinkedList chaining vs Red-Black Tree treeification)? → [Solution](02_java_collections_and_generics_faq.md#q7-hashmap-internals-capacity-load-factor-resizing-null-keys--collisions)
15. **`[Medium]`** What are the differences between HashMap, ConcurrentHashMap, and Hashtable? → [Solution](02_java_collections_and_generics_faq.md#q8-hashmap-vs-concurrenthashmap-vs-hashtable--concurrency-mechanics)
16. **`[Medium]`** What is the difference between WeakHashMap and HashMap, and how does it prevent memory leaks? → [Solution](02_java_collections_and_generics_faq.md#q9-weakhashmap-vs-hashmap)
17. **`[Medium]`** How does a TreeMap maintain sorting of its keys (Red-Black tree)? → [Solution](02_java_collections_and_generics_faq.md#q10-treemap-red-black-tree-sorting--null-keys)
18. **`[Medium]`** Can TreeMap have null keys? Why or why not? → [Solution](02_java_collections_and_generics_faq.md#q10-treemap-red-black-tree-sorting--null-keys)
19. **`[Medium]`** How do you use EnumSet and EnumMap in Java, and why are they backed by bit vectors? → [Solution](02_java_collections_and_generics_faq.md#q11-enumset-and-enummap)
20. **`[Medium]`** What is the key functional and structural difference between Queue and Deque? → [Solution](02_java_collections_and_generics_faq.md#q12-queue-vs-deque)
21. **`[Medium]`** How does PriorityQueue work internally in Java (Binary Min-Heap)? → [Solution](02_java_collections_and_generics_faq.md#q13-priorityqueue-internal-working)
22. **`[Medium]`** What is the difference between Fail-Fast and Fail-Safe iterators? → [Solution](02_java_collections_and_generics_faq.md#q16-fail-fast-vs-fail-safe-concurrentmodificationexception--safe-removal)
23. **`[Medium]`** What causes a ConcurrentModificationException, and how can you safely remove elements during iteration? → [Solution](02_java_collections_and_generics_faq.md#q16-fail-fast-vs-fail-safe-concurrentmodificationexception--safe-removal)
24. **`[Medium]`** How do you safely remove elements from a collection while iterating over it? → [Solution](02_java_collections_and_generics_faq.md#q16-fail-fast-vs-fail-safe-concurrentmodificationexception--safe-removal)
25. **`[Medium]`** What are stable sorting algorithms and in-place sorting algorithms (TimSort)? → [Solution](02_java_collections_and_generics_faq.md#q18-stable-vs-in-place-sorting-algorithms)
26. **`[Medium]`** How do you create an immutable collection in Java (Collections.unmodifiableList vs List.copyOf)? → [Solution](02_java_collections_and_generics_faq.md#q19-creating-truly-immutable-collections)
27. **`[Medium]`** How do you filter a list of employees to get those with a salary greater than 10,000? → [Solution](02_java_collections_and_generics_faq.md#q21-filter-employees-by-salary--10000)
28. **`[Medium]`** How do you rearrange a list so the minimum element is at index 0 and maximum is at the last index? → [Solution](02_java_collections_and_generics_faq.md#q22-rearrange-list-min-at-first-index-max-at-last-index)
29. **`[Medium]`** How do you find the most frequently occurring element in a list? → [Solution](02_java_collections_and_generics_faq.md#q23-finding-frequency-of-elements--most-frequent-element)
30. **`[Medium]`** How do you calculate the frequency of each element in a list using Streams? → [Solution](02_java_collections_and_generics_faq.md#q23-finding-frequency-of-elements--most-frequent-element)
31. **`[Medium]`** How do you sort a Map by its values in Java using Streams? → [Solution](02_java_collections_and_generics_faq.md#q25-sorting-a-map-by-values)
32. **`[Medium]`** Minimum meeting rooms problem using a Greedy approach and Min-Heap (Meeting Rooms II). → [Solution](02_java_collections_and_generics_faq.md#q26-meeting-rooms-ii-greedy-interval-scheduling-via-min-heap-priorityqueue)
33. **`[Medium]`** Finding the Nth-largest element using a bounded Min-Heap (PriorityQueue). → [Solution](02_java_collections_and_generics_faq.md#q27-finding-the-nth-largest-element-using-a-min-heap-priorityqueue)
34. **`[Medium]`** Can HashMap keys be mutable? Explain hashcode mutation pitfalls and memory leaks. → [Solution](02_java_collections_and_generics_faq.md#q28-can-hashmap-keys-be-mutable-key-mutation-pitfalls--memory-leaks)
35. **`[Hard]`** How does ConcurrentHashMap work internally in Java 8+ to achieve thread safety without locking the entire map (CAS + Node Synchronized)? → [Solution](02_java_collections_and_generics_faq.md#q8-hashmap-vs-concurrenthashmap-vs-hashtable--concurrency-mechanics)
36. **`[Hard]`** How would you handle large datasets in Java using Collections without exhausting heap memory? → [Solution](02_java_collections_and_generics_faq.md#q20-handling-large-datasets-in-memory-using-collections)
37. **`[Hard]`** How do you implement an LRU Cache in Java with O(1) operations using LinkedHashMap? → [Solution](02_java_collections_and_generics_faq.md#q24-lru-cache-implementation-with-o1-operations)
38. **`[Hard]`** Explain HashMap rehashing internals, threshold doubling triggers, and bitwise power-of-two index distribution. → [Solution](02_java_collections_and_generics_faq.md#q29-hashmap-rehashing-internals-threshold-triggers--bitwise-power-of-two-indexing)
39. **`[Medium]`** What is the PECS rule in Java Generics, and how do you write robust, idiomatic code using Optional without anti-patterns? → [Solution](02_java_collections_and_generics_faq.md#q30-java-generics-pecs-rule--extends-t-vs--super-t--optional-idiomatic-usage)

---

## 03 – Modern Java Features, Lambdas & Functional Streams

*Companion Deep-Dive Guide: [`03_modern_java_streams_and_records_faq.md`](03_modern_java_streams_and_records_faq.md) (12 Questions & Production Solutions)*

1. **`[Easy]`** What is a functional interface in Java, and what are some commonly used built-in functional interfaces? → [Solution](03_modern_java_streams_and_records_faq.md#q1-functional-interfaces-functionalinterface-built-ins--predicate)
2. **`[Easy]`** What is the significance of the @FunctionalInterface annotation in Java? → [Solution](03_modern_java_streams_and_records_faq.md#q1-functional-interfaces-functionalinterface-built-ins--predicate)
3. **`[Easy]`** What is the role and purpose of the java.util.function package? → [Solution](03_modern_java_streams_and_records_faq.md#q1-functional-interfaces-functionalinterface-built-ins--predicate)
4. **`[Easy]`** What is a Predicate interface, and how is it used in Java? → [Solution](03_modern_java_streams_and_records_faq.md#q1-functional-interfaces-functionalinterface-built-ins--predicate)
5. **`[Easy]`** What are method references in Java and how do they relate to lambdas? → [Solution](03_modern_java_streams_and_records_faq.md#q2-method-references-in-java)
6. **`[Easy]`** What is Optional used for, and how does it help prevent NullPointerExceptions? → [Solution](03_modern_java_streams_and_records_faq.md#q3-optional-architecture-and-best-practices-for-npe-prevention)
7. **`[Medium]`** What is a Stream in Java 8, and how does it differ from a Collection? → [Solution](03_modern_java_streams_and_records_faq.md#q4-streams-vs-collections--internal-pipeline-mechanics)
8. **`[Medium]`** How do Java Streams work internally (Pipeline Fusion, Intermediate vs Terminal Operations)? → [Solution](03_modern_java_streams_and_records_faq.md#q4-streams-vs-collections--internal-pipeline-mechanics)
9. **`[Medium]`** What is the difference between map() and flatMap() in the Java Stream API? → [Solution](03_modern_java_streams_and_records_faq.md#q5-map-vs-flatmap)
10. **`[Medium]`** How do you filter a list of employee names starting with 'SU' using the Stream API? → [Solution](03_modern_java_streams_and_records_faq.md#q7-string-filtering--sorting-with-stream-api)
11. **`[Medium]`** How do you find employee names starting with 'a' or 'A' and sort them using Java Streams? → [Solution](03_modern_java_streams_and_records_faq.md#q7-string-filtering--sorting-with-stream-api)
12. **`[Medium]`** How do you count the number of vowels in a string using Java 8 Streams? → [Solution](03_modern_java_streams_and_records_faq.md#q8-counting-vowels-in-a-string-using-streams)
13. **`[Medium]`** How do you find the second highest integer in an unsorted list using the Stream API? → [Solution](03_modern_java_streams_and_records_faq.md#q9-finding-second-highest-integer-in-an-unsorted-list)
14. **`[Medium]`** Write a Java Stream program to find the maximum salary in each department. → [Solution](03_modern_java_streams_and_records_faq.md#q10-finding-the-highest--maximum-salary-in-each-department-using-java-streams)
15. **`[Medium]`** Group employees into dynamic age ranges like 20-30 and 30-40 generically using Streams. → [Solution](03_modern_java_streams_and_records_faq.md#q11-group-employees-into-dynamic-age-ranges-generically-using-streams)
16. **`[Medium]`** Find the first unique (non-repeating) character in a String using Stream API. → [Solution](03_modern_java_streams_and_records_faq.md#q12-find-the-first-non-repeating-unique-character-in-a-string-using-stream-api)
17. **`[Hard]`** How does the Stream API handle parallelism, and why can parallel streams degrade production latency under I/O workloads? → [Solution](03_modern_java_streams_and_records_faq.md#q6-parallel-streams--forkjoinpool-concurrency)

---

## 04 – Multithreading, Concurrency & Asynchronous Programming

*Companion Deep-Dive Guide: [`04_java_concurrency_and_multithreading_faq.md`](04_java_concurrency_and_multithreading_faq.md) (15 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between Runnable and Callable interfaces in Java? → [Solution](04_java_concurrency_and_multithreading_faq.md#q1-runnable-vs-callable--calling-run-vs-start)
2. **`[Easy]`** What happens when you call run() instead of start() on a Thread? → [Solution](04_java_concurrency_and_multithreading_faq.md#q1-runnable-vs-callable--calling-run-vs-start)
3. **`[Easy]`** Different ways to create threads in Java, with modern examples (Thread, Runnable, Callable, Virtual Threads). → [Solution](04_java_concurrency_and_multithreading_faq.md#q13-modern-thread-creation-strategies-platform-threads-executorservice--java-21-virtual-threads)
4. **`[Medium]`** What is the difference between wait() and sleep() methods in Java multithreading? → [Solution](04_java_concurrency_and_multithreading_faq.md#q3-the-synchronized-monitor--sleep-vs-wait)
5. **`[Medium]`** How does the synchronized monitor work in Java (monitorenter and monitorexit bytecode)? → [Solution](04_java_concurrency_and_multithreading_faq.md#q3-the-synchronized-monitor--sleep-vs-wait)
6. **`[Medium]`** What is the volatile keyword, and how does it guarantee visibility without atomicity? → [Solution](04_java_concurrency_and_multithreading_faq.md#q4-volatile-vs-atomicinteger-vs-synchronized)
7. **`[Medium]`** What is the difference between volatile and Atomic classes such as AtomicInteger and AtomicReference (CAS operations)? → [Solution](04_java_concurrency_and_multithreading_faq.md#q4-volatile-vs-atomicinteger-vs-synchronized)
8. **`[Medium]`** What happens when multiple threads modify the same shared mutable object without synchronization? → [Solution](04_java_concurrency_and_multithreading_faq.md#q12-shared-mutable-object-hazards-race-conditions-memory-visibility--jmm)
9. **`[Medium]`** Difference between synchronized and ReentrantLock. What are the feature trade-offs? → [Solution](04_java_concurrency_and_multithreading_faq.md#q14-synchronized-vs-reentrantlock-feature-matrix--trade-offs)
10. **`[Medium]`** Why might the JVM fail to terminate after the main() method completes (Non-daemon threads)? → [Solution](04_java_concurrency_and_multithreading_faq.md#q2-why-the-jvm-fails-to-terminate-after-main-completes)
11. **`[Medium]`** Explain what happens when multiple threads update shared state (e.g. bank balance) and how to fix race conditions. → [Solution](04_java_concurrency_and_multithreading_faq.md#q8-executorservice-architecture-saturated-queues--silent-failures)
12. **`[Medium]`** What is the difference between Future and CompletableFuture in Java? → [Solution](04_java_concurrency_and_multithreading_faq.md#q9-future-vs-completablefuture-thenapply-vs-thencompose--concurrency-optimization)
13. **`[Medium]`** What is the difference between thenApply() and thenCompose() in CompletableFuture pipelines? → [Solution](04_java_concurrency_and_multithreading_faq.md#q9-future-vs-completablefuture-thenapply-vs-thencompose--concurrency-optimization)
14. **`[Hard]`** Show how two threads can deadlock in Java, how you detect it via thread dumps, and how you prevent it. → [Solution](04_java_concurrency_and_multithreading_faq.md#q5-deadlock-conditions-thread-dumps--prevention)
15. **`[Hard]`** How does ThreadPoolExecutor work internally (corePoolSize, maximumPoolSize, workQueue, and RejectionExecutionHandler)? → [Solution](04_java_concurrency_and_multithreading_faq.md#q6-fixing-inconsistent-shared-state-bank-balance-race-condition)
16. **`[Hard]`** Why do tasks submitted to an ExecutorService queue up unexpectedly even though the maximum pool size is large? → [Solution](04_java_concurrency_and_multithreading_faq.md#q6-fixing-inconsistent-shared-state-bank-balance-race-condition)
17. **`[Hard]`** Why might ExecutorService tasks fail silently without logging errors, and how can this be resolved? → [Solution](04_java_concurrency_and_multithreading_faq.md#q6-fixing-inconsistent-shared-state-bank-balance-race-condition)
18. **`[Hard]`** Why does parallel stream execution degrade production latency when mixing CPU and I/O tasks? → [Solution](04_java_concurrency_and_multithreading_faq.md#q10-why-parallel-streams-degrade-production-latency)
19. **`[Hard]`** How would you optimize an asynchronous pipeline using CompletableFuture with custom thread pools? → [Solution](04_java_concurrency_and_multithreading_faq.md#q9-future-vs-completablefuture-thenapply-vs-thencompose--concurrency-optimization)
20. **`[Hard]`** Call API 1 and API 2 asynchronously, call API 3 synchronously, then wait for 1 and 2. Plain Java implementation. → [Solution](04_java_concurrency_and_multithreading_faq.md#q11-orchestrating-asynchronous-and-synchronous-remote-calls-with-completablefuture)
21. **`[Expert]`** Under heavy traffic, your application spawns thousands of platform threads causing OOM and CPU thrashing. How do you re-architect with Virtual Threads and bounded queues? → [Solution](04_java_concurrency_and_multithreading_faq.md#q8-executorservice-architecture-saturated-queues--silent-failures)
22. **`[Medium]`** What is the difference between ReentrantLock and ReentrantReadWriteLock? When would you prefer one over the other, and how does lock downgrading work? → [Solution](04_java_concurrency_and_multithreading_faq.md#q15-reentrantlock-vs-reentrantreadwritelock-readwrite-contention--lock-downgrading)

---

## 05 – Spring Framework, Spring Boot & REST APIs

*Companion Deep-Dive Guide: [`05_spring_boot_and_rest_apis_faq.md`](05_spring_boot_and_rest_apis_faq.md) (37 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between Spring Framework and Spring Boot? → [Solution](05_spring_boot_and_rest_apis_faq.md#q1-spring-framework-vs-spring-boot)
2. **`[Easy]`** What is the ApplicationContext in Spring and how does it relate to BeanFactory? → [Solution](05_spring_boot_and_rest_apis_faq.md#q2-what-is-applicationcontext-in-spring)
3. **`[Easy]`** Difference between @Component, @Service, @Repository, and @Controller stereotypes. → [Solution](05_spring_boot_and_rest_apis_faq.md#q4-stereotype-annotations-component-service-repository-controller--scanning)
4. **`[Easy]`** What is the difference between @Primary and @Qualifier annotations in Spring? → [Solution](05_spring_boot_and_rest_apis_faq.md#q5-primary-vs-qualifier)
5. **`[Easy]`** What is the difference between @Bean and @Autowired annotations? → [Solution](05_spring_boot_and_rest_apis_faq.md#q6-bean-vs-autowired-in-dependency--database-management)
6. **`[Easy]`** What are the different Spring Bean scopes (Singleton, Prototype, Request, Session)? → [Solution](05_spring_boot_and_rest_apis_faq.md#q8-spring-bean-scopes--switching-via-scope)
7. **`[Easy]`** What is the difference between prototype scope and request scope in Spring web applications? → [Solution](05_spring_boot_and_rest_apis_faq.md#q8-spring-bean-scopes--switching-via-scope)
8. **`[Medium]`** Explain IoC (Inversion of Control) and Dependency Injection internals and constructor injection best practices. → [Solution](05_spring_boot_and_rest_apis_faq.md#q3-dependency-injection-internals--best-practices-in-spring-boot)
9. **`[Medium]`** Why is constructor injection preferred over field injection in production Spring Boot applications? → [Solution](05_spring_boot_and_rest_apis_faq.md#q3-dependency-injection-internals--best-practices-in-spring-boot)
10. **`[Medium]`** How does Spring component scanning work across packages starting from @SpringBootApplication? → [Solution](05_spring_boot_and_rest_apis_faq.md#q4-stereotype-annotations-component-service-repository-controller--scanning)
11. **`[Medium]`** What are circular dependencies in Spring beans, why are they an architectural smell, and how are they resolved? → [Solution](05_spring_boot_and_rest_apis_faq.md#q7-the-complete-spring-bean-lifecycle)
12. **`[Medium]`** What are Spring Profiles and how do you organize environment-specific configurations? → [Solution](05_spring_boot_and_rest_apis_faq.md#q11-multi-environment-spring-profiles)
13. **`[Medium]`** What is ViewResolver in Spring MVC and why is it bypassed in RESTful architectures? → [Solution](05_spring_boot_and_rest_apis_faq.md#q12-viewresolver-in-spring-mvc-architecture)
14. **`[Medium]`** Explain Spring Web exception handling annotations (@ExceptionHandler, @ControllerAdvice, @ResponseStatus). → [Solution](05_spring_boot_and_rest_apis_faq.md#q13-spring-web-exception-handling-annotations)
15. **`[Medium]`** How do you design a clean modular Spring Boot architecture across API, domain, service, and persistence layers? → [Solution](05_spring_boot_and_rest_apis_faq.md#q15-architectural-approach-to-developing-a-new-spring-boot-module)
16. **`[Medium]`** Explain the complete Spring Bean Lifecycle pipeline from instantiation to destruction. → [Solution](05_spring_boot_and_rest_apis_faq.md#q17-complete-spring-bean-lifecycle-pipeline)
17. **`[Medium]`** What is @EnableAutoConfiguration and how does it work internally with conditional annotations? → [Solution](05_spring_boot_and_rest_apis_faq.md#q18-how-enableautoconfiguration-works-internally-with-conditional-annotations)
18. **`[Hard]`** How does Spring Boot auto-configuration work internally using @Conditional annotations and spring.factories? → [Solution](05_spring_boot_and_rest_apis_faq.md#q9-springbootapplication--auto-configuration-internals)
19. **`[Hard]`** How do you create a custom Spring Boot auto-configuration starter module? → [Solution](05_spring_boot_and_rest_apis_faq.md#q9-springbootapplication--auto-configuration-internals)
20. **`[Hard]`** How do you securely configure and harden Spring Boot Actuator endpoints in production? → [Solution](05_spring_boot_and_rest_apis_faq.md#q14-spring-boot-actuator-in-production--endpoint-security)
21. **`[Hard]`** Explain what happens internally when a Spring Boot application starts (SpringApplication.run bootstrapping sequence). → [Solution](05_spring_boot_and_rest_apis_faq.md#q16-what-happens-internally-when-a-spring-boot-application-starts-springapplicationrun)
22. **`[Expert]`** Explain how Spring creates CGLIB and JDK dynamic proxies for @Transactional and @Async beans during post-processing. → [Solution](05_spring_boot_and_rest_apis_faq.md#q10-property-injection-via-value-vs-configurationproperties)
23. **`[Easy]`** Is REST architecture stateless or stateful, and why is statelessness essential for horizontal scalability? → [Solution](05_spring_boot_and_rest_apis_faq.md#q19-is-rest-architecture-stateless-or-stateful-and-why)
24. **`[Easy]`** What is the difference between @Controller and @RestController in Spring Boot? → [Solution](05_spring_boot_and_rest_apis_faq.md#q22-controller-vs-restcontroller--http-request-mapping-annotations)
25. **`[Easy]`** What are the common HTTP request mapping annotations in Spring (@GetMapping, @PostMapping, etc.)? → [Solution](05_spring_boot_and_rest_apis_faq.md#q22-controller-vs-restcontroller--http-request-mapping-annotations)
26. **`[Easy]`** Difference between @RequestParam, @PathVariable, and @RequestBody in Spring MVC. → [Solution](05_spring_boot_and_rest_apis_faq.md#q23-parameter-binding-requestparam-vs-pathvariable-vs-requestbody)
27. **`[Easy]`** What is the difference between PUT and PATCH HTTP methods? → [Solution](05_spring_boot_and_rest_apis_faq.md#q20-put-vs-patch--designing-idempotent-api-operations)
28. **`[Easy]`** Comprehensive HTTP status codes taxonomy: When do you return 200, 201, 202, 204, 400, 401, 403, 404, 409, 422, 500, 503? → [Solution](05_spring_boot_and_rest_apis_faq.md#q21-comprehensive-http-status-codes-taxonomy)
29. **`[Easy]`** What headers do you typically pass in an outbound RestTemplate or WebClient request in enterprise systems? → [Solution](05_spring_boot_and_rest_apis_faq.md#q34-standard-enterprise-headers-in-resttemplate--webclient-outbound-requests)
30. **`[Easy]`** What is the difference between @ControllerAdvice and @RestControllerAdvice? → [Solution](05_spring_boot_and_rest_apis_faq.md#q36-controlleradvice-vs-restcontrolleradvice--rfc-7807-problemdetail)
31. **`[Medium]`** How do you design idempotent operations in RESTful APIs using unique Idempotency Keys? → [Solution](05_spring_boot_and_rest_apis_faq.md#q20-put-vs-patch--designing-idempotent-api-operations)
32. **`[Medium]`** How do you implement global exception handling in Spring Boot REST APIs using @RestControllerAdvice and ProblemDetail? → [Solution](05_spring_boot_and_rest_apis_faq.md#q25-graceful-error-handling--global-exception-management-via-restcontrolleradvice)
33. **`[Medium]`** How do you perform payload validation in Spring Boot using Hibernate Validator (@Valid, @NotNull, @Size)? → [Solution](05_spring_boot_and_rest_apis_faq.md#q24-the-dto-pattern--request-flow-across-controller-service-and-repository)
34. **`[Medium]`** How do you handle file uploads in Spring Boot using MultipartFile securely? → [Solution](05_spring_boot_and_rest_apis_faq.md#q24-the-dto-pattern--request-flow-across-controller-service-and-repository)
35. **`[Medium]`** How do you enable and configure Cross-Origin Resource Sharing (CORS) in Spring Boot? → [Solution](05_spring_boot_and_rest_apis_faq.md#q25-graceful-error-handling--global-exception-management-via-restcontrolleradvice)
36. **`[Medium]`** What are the differences between RestTemplate, WebClient, and declarative OpenFeign in Spring Boot? → [Solution](05_spring_boot_and_rest_apis_faq.md#q27-http-clients-resttemplate-vs-webclient-vs-feignclient)
37. **`[Medium]`** How do you implement client-side load balancing and circuit breaking when using OpenFeign? → [Solution](05_spring_boot_and_rest_apis_faq.md#q28-api-versioning-strategies-in-production)
38. **`[Medium]`** How would you design a REST API endpoint to fetch the last N orders for a customer using Spring Boot and Spring Data JPA? → [Solution](05_spring_boot_and_rest_apis_faq.md#q32-designing-a-rest-endpoint-for-last-n-orders-spring-boot--spring-data-jpa)
39. **`[Medium]`** What are the standard RESTful API versioning strategies in production (URI path vs Query param vs Header vs Media Type)? → [Solution](05_spring_boot_and_rest_apis_faq.md#q35-restful-api-versioning-strategies-in-production)
40. **`[Hard]`** How do you secure REST APIs using Spring Security and JWT tokens? → [Solution](05_spring_boot_and_rest_apis_faq.md#q29-how-spring-boot-handles-high-concurrency-requests)
41. **`[Hard]`** What are the architectural trade-offs between REST, gRPC, and GraphQL for enterprise systems? → [Solution](05_spring_boot_and_rest_apis_faq.md#q33-rest-vs-grpc-vs-graphql-architectural-comparison)
42. **`[Hard]`** How do you design and test idempotent payment webhook ingestion endpoints? → [Solution](05_spring_boot_and_rest_apis_faq.md#q26-handling-inconsistent-external-api-calls-nulls--failures-gracefully)
43. **`[Expert]`** How would you design a high-throughput streaming REST API with Server-Sent Events (SSE) and reactive WebFlux backpressure? → [Solution](05_spring_boot_and_rest_apis_faq.md#q30-rate-limiting--throttling-in-spring-boot-apis)
44. **`[Expert]`** Design an enterprise API Gateway rate limiting architecture using Redis Token Bucket with sub-millisecond overhead. → [Solution](05_spring_boot_and_rest_apis_faq.md#q31-configuring-spring-security-for-jwt-on-a-subset-of-endpoints)
45. **`[Hard]`** How do you design idempotent REST APIs using Idempotency-Keys to prevent duplicate entity creation on network timeouts or retries? → [Solution](05_spring_boot_and_rest_apis_faq.md#q37-idempotent-api-design-with-idempotency-keys-to-prevent-duplicate-postings-on-client-timeouts)

---

## 06 – Spring Security, OAuth2, OpenID Connect & JWT Architecture

*Companion Deep-Dive Guide: [`06_security_and_cryptography_faq.md`](06_security_and_cryptography_faq.md) (17 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between Authentication and Authorization? → [Solution](06_security_and_cryptography_faq.md#q1-authentication-vs-authorization)
2. **`[Easy]`** What is the difference between BCryptPasswordEncoder and plain text password hashing? → [Solution](06_security_and_cryptography_faq.md#q4-bcryptpasswordencoder-vs-plain-text-passwords)
3. **`[Easy]`** What are the differences between @PreAuthorize, @Secured, and @RolesAllowed in method security? → [Solution](06_security_and_cryptography_faq.md#q5-method-level-security-preauthorize-vs-secured-vs-rolesallowed)
4. **`[Easy]`** What is CSRF and why is it disabled for stateless RESTful APIs? → [Solution](06_security_and_cryptography_faq.md#q6-spring-security-csrf-protection-mechanics)
5. **`[Easy]`** What is the difference between OAuth2 and Basic Authentication? → [Solution](06_security_and_cryptography_faq.md#q13-oauth2-vs-basic-authentication)
6. **`[Medium]`** How does Spring Security's SecurityFilterChain execute internally as a chain of servlet filters? → [Solution](06_security_and_cryptography_faq.md#q2-spring-security-internal-architecture-filter-chain--authenticationmanager)
7. **`[Medium]`** What is the role of AuthenticationManager, AuthenticationProvider, and SecurityContextHolder? → [Solution](06_security_and_cryptography_faq.md#q2-spring-security-internal-architecture-filter-chain--authenticationmanager)
8. **`[Medium]`** How do you implement a custom UserDetailsService and AuthenticationProvider in Spring Security? → [Solution](06_security_and_cryptography_faq.md#q3-userdetailsservice--custom-authenticationprovider)
9. **`[Medium]`** What is SpEL (Spring Expression Language) and how is it used in @PreAuthorize("hasRole('ADMIN')")? → [Solution](06_security_and_cryptography_faq.md#q5-method-level-security-preauthorize-vs-secured-vs-rolesallowed)
10. **`[Medium]`** What is CORS and why must it be configured before Spring Security filter evaluation? → [Solution](06_security_and_cryptography_faq.md#q7-remember-me-authentication-architecture)
11. **`[Medium]`** Explain the structure of a JSON Web Token (Header, Payload, Signature) and how RS256 asymmetric signing works. → [Solution](06_security_and_cryptography_faq.md#q8-why-use-jwt-tokens-instead-of-stateful-sessions)
12. **`[Medium]`** How do you securely store JWT tokens on the client to protect against XSS and CSRF (HttpOnly cookie)? → [Solution](06_security_and_cryptography_faq.md#q10-client-side-jwt-storage--early-token-revocation)
13. **`[Medium]`** What happens if a JWT token expires and how do you implement short-lived access tokens with Refresh Tokens? → [Solution](06_security_and_cryptography_faq.md#q11-expired-tokens-refresh-token-rotation--concurrency)
14. **`[Medium]`** What are common JWT security vulnerabilities (alg: none, weak secret keys) and how do you mitigate them? → [Solution](06_security_and_cryptography_faq.md#q12-jwt-security-risks--mitigations)
15. **`[Medium]`** Explain the OAuth2 Client Credentials Grant and when to use it for backend microservice-to-microservice communication. → [Solution](06_security_and_cryptography_faq.md#q15-oauth2-client-credentials-grant-for-microservices)
16. **`[Hard]`** Explain the OAuth2 Authorization Code Flow with PKCE (Proof Key for Code Exchange) for mobile and single-page applications. → [Solution](06_security_and_cryptography_faq.md#q14-oauth2-authorization-code-flow-with-pkce)
17. **`[Hard]`** How do you securely propagate and validate JWT tokens across distributed microservices using Feign and JWKS? → [Solution](06_security_and_cryptography_faq.md#q16-cross-service-jwt-propagation--verification-in-distributed-microservices)
18. **`[Hard]`** How do you implement distributed JWT revocation before expiration using a Redis token blacklist? → [Solution](06_security_and_cryptography_faq.md#q10-client-side-jwt-storage--early-token-revocation)
19. **`[Hard]`** How does refresh token rotation with reuse detection prevent token theft and trigger blast-radius invalidation? → [Solution](06_security_and_cryptography_faq.md#q17-refresh-token-reuse-detection--blast-radius-revocation-workflows)
20. **`[Expert]`** Design an enterprise single-sign-on (SSO) architecture supporting multi-tenant OAuth2 and OpenID Connect with Keycloak and Azure AD. → [Solution](06_security_and_cryptography_faq.md#q2-spring-security-internal-architecture-filter-chain--authenticationmanager)
21. **`[Expert]`** How would you design a zero-trust mutual TLS (mTLS) and token-exchange microservices security fabric? → [Solution](06_security_and_cryptography_faq.md#q15-oauth2-client-credentials-grant-for-microservices)
22. **`[Expert]`** A security penetration test discovers that user permissions updated in the database take 15 minutes to reflect in JWT tokens. How do you resolve this without centralized session lookups? → [Solution](06_security_and_cryptography_faq.md#q10-client-side-jwt-storage--early-token-revocation)
---

## 07 – Relational Databases, SQL Optimization, JPA & Hibernate ORM

*Companion Deep-Dive Guide: [`07_databases_jpa_and_hibernate_faq.md`](07_databases_jpa_and_hibernate_faq.md) (25 Questions & Production Solutions)*

1. **`[Easy]`** What are the ACID properties in relational database management systems? → [Solution](07_databases_jpa_and_hibernate_faq.md#q1-acid-properties--database-transaction-isolation-levels)
2. **`[Easy]`** What is the difference between INNER JOIN, LEFT JOIN, RIGHT JOIN, and FULL OUTER JOIN? → [Solution](07_databases_jpa_and_hibernate_faq.md#q2-sql-joins-inner-left-right-and-full-outer)
3. **`[Easy]`** Write an SQL query to fetch the second-highest salary from an Employee table with strict NULL handling. → [Solution](07_databases_jpa_and_hibernate_faq.md#q3-practical-sql-queries-second-highest-salary--department-wise-averages)
4. **`[Easy]`** Write an SQL query to calculate department-wise average salaries and filter departments with average > 50,000. → [Solution](07_databases_jpa_and_hibernate_faq.md#q3-practical-sql-queries-second-highest-salary--department-wise-averages)
5. **`[Easy]`** What is the difference between Primary Key and Unique Key? → [Solution](07_databases_jpa_and_hibernate_faq.md#q1-acid-properties--database-transaction-isolation-levels)
6. **`[Easy]`** What is the difference between DELETE and TRUNCATE statements in SQL? → [Solution](07_databases_jpa_and_hibernate_faq.md#q1-acid-properties--database-transaction-isolation-levels)
7. **`[Easy]`** What are the differences between JpaRepository, CrudRepository, and PagingAndSortingRepository in Spring Data? → [Solution](07_databases_jpa_and_hibernate_faq.md#q21-jparepository-vs-crudrepository-vs-pagingandsortingrepository)
8. **`[Medium]`** Explain database transaction isolation levels (READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE) and anomalies. → [Solution](07_databases_jpa_and_hibernate_faq.md#q1-acid-properties--database-transaction-isolation-levels)
9. **`[Medium]`** What is the difference between Clustered Index and Non-Clustered Index in relational databases? → [Solution](07_databases_jpa_and_hibernate_faq.md#q4-clustered-vs-non-clustered-indexes--slow-query-optimization)
10. **`[Medium]`** How do you identify and optimize slow SQL queries using EXPLAIN execution plans and indexes? → [Solution](07_databases_jpa_and_hibernate_faq.md#q4-clustered-vs-non-clustered-indexes--slow-query-optimization)
11. **`[Medium]`** How do you invoke stored procedures and handle result sets in Java using JDBC CallableStatement? → [Solution](07_databases_jpa_and_hibernate_faq.md#q5-invoking-stored-procedures-via-jdbc)
12. **`[Medium]`** How do you configure multiple DataSources (e.g. Primary and Secondary databases) in a Spring Boot application? → [Solution](07_databases_jpa_and_hibernate_faq.md#q6-connecting-multiple-datasources-in-spring-boot)
13. **`[Medium]`** Explain Hibernate Entity Lifecycle states: Transient, Persistent (Managed), Detached, and Removed. → [Solution](07_databases_jpa_and_hibernate_faq.md#q7-jpa-primary-key-annotations-id--generatedvalue)
14. **`[Medium]`** What is the difference between FetchType.LAZY and FetchType.EAGER in JPA entities, and why is EAGER considered an anti-pattern? → [Solution](07_databases_jpa_and_hibernate_faq.md#q8-hibernate-entity-lifecycle-states)
15. **`[Medium]`** What is the difference between First-Level Cache (Session) and Second-Level Cache (SessionFactory/Ehcache/Redis) in Hibernate? → [Solution](07_databases_jpa_and_hibernate_faq.md#q10-lazy-vs-eager-lazyinitializationexception--the-n1-select-problem)
16. **`[Medium]`** How does Spring @Transactional work internally using Spring AOP dynamic proxies, and why does self-invocation fail? → [Solution](07_databases_jpa_and_hibernate_faq.md#q10-lazy-vs-eager-lazyinitializationexception--the-n1-select-problem)
17. **`[Medium]`** What are the common transaction propagation levels in Spring (REQUIRED, REQUIRES_NEW, SUPPORTS, MANDATORY)? → [Solution](07_databases_jpa_and_hibernate_faq.md#q14-spring-transactional-propagation-levels--pitfalls)
18. **`[Medium]`** What is the difference between Optimistic Locking (@Version) and Pessimistic Locking (PESSIMISTIC_WRITE)? → [Solution](07_databases_jpa_and_hibernate_faq.md#q15-optimistic-vs-pessimistic-locking--the-train-seat-booking-race-condition)
19. **`[Medium]`** How do you implement pagination and sorting efficiently in Spring Data JPA using Pageable and Slice? → [Solution](07_databases_jpa_and_hibernate_faq.md#q16-pagination-and-sorting-in-spring-data-jpa)
20. **`[Medium]`** How is rollback managed for a series of SQL statements across transaction boundaries and savepoints? → [Solution](07_databases_jpa_and_hibernate_faq.md#q20-managing-transaction-rollbacks-across-a-series-of-sql-statements)
21. **`[Medium]`** Write an SQL query to delete duplicate rows from a table while keeping one copy. → [Solution](07_databases_jpa_and_hibernate_faq.md#q22-deleting-duplicate-rows-while-retaining-exactly-one-copy-in-sql)
22. **`[Hard]`** What is the N+1 select problem in ORM frameworks, and what are the 3 production ways to resolve it (JOIN FETCH, EntityGraph, BatchSize)? → [Solution](07_databases_jpa_and_hibernate_faq.md#q9-criteria-api-vs-jpql)
23. **`[Hard]`** How do you resolve the train seat or hotel room booking race condition where two users attempt to reserve the same seat simultaneously? → [Solution](07_databases_jpa_and_hibernate_faq.md#q15-optimistic-vs-pessimistic-locking--the-train-seat-booking-race-condition)
24. **`[Hard]`** How will you make sure multiple instances of a cron job worker don't update the same row (ShedLock vs SELECT FOR UPDATE SKIP LOCKED)? → [Solution](07_databases_jpa_and_hibernate_faq.md#q18-multi-instance-cron-worker-row-concurrency-shedlock-vs-select-for-update-skip-locked)
25. **`[Hard]`** Two concurrent transactions occasionally overwrite each other's updates. How do you diagnose the Lost Update anomaly and choose the right concurrency strategy? → [Solution](07_databases_jpa_and_hibernate_faq.md#q19-lost-update-anomaly-optimistic-vs-pessimistic-locking-strategy-selection)
26. **`[Hard]`** How does Hibernate dirty checking work internally, and why does marking transactions readOnly = true yield major memory and CPU gains? → [Solution](07_databases_jpa_and_hibernate_faq.md#q10-lazy-vs-eager-lazyinitializationexception--the-n1-select-problem)
27. **`[Expert]`** How would you design and implement a Multi-Tenant JPA persistence architecture using Schema-Per-Tenant or Database-Per-Tenant with dynamic routing? → [Solution](07_databases_jpa_and_hibernate_faq.md#q17-multi-tenancy-architecture-in-jpa)
28. **`[Medium]`** How do database indexes work internally (B-Tree vs Hash), what is the composite index leftmost prefix rule, and why do functions in WHERE clauses break indexes? → [Solution](07_databases_jpa_and_hibernate_faq.md#q23-database-indexing-mechanics--composite-index-leftmost-prefix-rule)
29. **`[Medium]`** What are the differences between Stored Procedures and Database Triggers? Compare execution performance, business logic placement, and debugging trade-offs. → [Solution](07_databases_jpa_and_hibernate_faq.md#q24-stored-procedures-vs-database-triggers-performance--architecture-trade-offs)
30. **`[Medium]`** Write an SQL query to find employees whose salary is greater than their manager's salary using a self-join. → [Solution](07_databases_jpa_and_hibernate_faq.md#q25-sql-self-join-finding-employees-earning-more-than-their-managers)
---

## 08 – Distributed Messaging Systems: Apache Kafka & RabbitMQ

*Companion Deep-Dive Guide: [`08_caching_redis_and_kafka_faq.md`](08_caching_redis_and_kafka_faq.md) (11 Questions & Production Solutions)*

1. **`[Easy]`** What is a message broker service and why is it used in enterprise Java architectures? → [Solution](08_caching_redis_and_kafka_faq.md#q1-message-broker-services-in-enterprise-java-architecture)
2. **`[Easy]`** What is an Exchange in AMQP / RabbitMQ and what are the different exchange types (Direct, Fanout, Topic, Headers)? → [Solution](08_caching_redis_and_kafka_faq.md#q2-what-is-an-exchange-in-amqp--rabbitmq)
3. **`[Medium]`** What is the difference between Apache Kafka and RabbitMQ? When would you choose each for an enterprise architecture? → [Solution](08_caching_redis_and_kafka_faq.md#q3-kafka-vs-rabbitmq-core-differences--selection-criteria)
4. **`[Medium]`** How do Kafka partitions enable horizontal scalability and what is a consumer group? → [Solution](08_caching_redis_and_kafka_faq.md#q4-kafka-architecture-brokers-partitions-consumer-groups--scalability)
5. **`[Medium]`** How do you integrate Apache Kafka with Spring Boot using KafkaTemplate and @KafkaListener? → [Solution](08_caching_redis_and_kafka_faq.md#q5-integrating-apache-kafka-with-spring-boot)
6. **`[Medium]`** How do consumer groups, partition rebalancing (Cooperative Sticky), and offset commits (auto vs manual) work during consumer failure? → [Solution](08_caching_redis_and_kafka_faq.md#q9-consumer-groups-partition-rebalancing--manual-offset-commit-strategies)
7. **`[Hard]`** How do you implement Dead Letter Queues (DLQ) and retry policies in Spring Kafka using @RetryableTopic? → [Solution](08_caching_redis_and_kafka_faq.md#q6-dead-letter-queues-dlq-retry-policies--mitigating-slow-consumers)
8. **`[Hard]`** What causes slow consumers and heartbeat timeouts in Kafka (max.poll.interval.ms exceeded), and how do you mitigate partition rebalances? → [Solution](08_caching_redis_and_kafka_faq.md#q6-dead-letter-queues-dlq-retry-policies--mitigating-slow-consumers)
9. **`[Hard]`** How does Kafka interact with databases using Change Data Capture (Debezium), Kafka Connect, and the Transactional Outbox pattern? → [Solution](08_caching_redis_and_kafka_faq.md#q7-how-kafka-interacts-with-databases-cdc-debezium-kafka-connect--transactional-outbox)
10. **`[Hard]`** Explain Kafka Schema Registry architecture, Avro binary serialization, and schema evolution compatibility modes (BACKWARD, FORWARD, FULL). → [Solution](08_caching_redis_and_kafka_faq.md#q8-kafka-schema-registry-architecture-avro-serialization--schema-evolution)
11. **`[Expert]`** Design an end-to-end exactly-once event streaming pipeline in Java: idempotent producer, Kafka transactions, and transactional consumer-database writes. → [Solution](08_caching_redis_and_kafka_faq.md#q4-kafka-architecture-brokers-partitions-consumer-groups--scalability)
12. **`[Hard]`** How do you diagnose and resolve Kafka consumer lag in production? Walk through partition rebalancing, consumer scaling, and batch commit bottlenecks. → [Solution](08_caching_redis_and_kafka_faq.md#q10-kafka-consumer-lag-diagnosis--resolution-playbook)
13. **`[Hard]`** How do producer acknowledgements (`acks=0`, `1`, `all`) interact with broker `min.insync.replicas`? Explain the trade-offs between durability and latency. → [Solution](08_caching_redis_and_kafka_faq.md#q11-kafka-producer-acknowledgements-acks-in-sync-replicas-mininsyncreplicas--durability-trade-offs)
---

## 09 – Microservices Architecture, Distributed Systems & System Design

*Companion Deep-Dive Guide: [`09_microservices_and_system_design_faq.md`](09_microservices_and_system_design_faq.md) (25 Questions & Production Solutions)*

1. **`[Easy]`** What are microservices and what are their primary advantages and challenges over a monolith? → [Solution](09_microservices_and_system_design_faq.md#q1-microservices-advantages--challenges--ddd-bounded-contexts)
2. **`[Easy]`** What is the role of an API Gateway in microservices architecture? → [Solution](09_microservices_and_system_design_faq.md#q2-service-discovery-load-balancing-eureka-vs-consul--the-api-gateway-pattern)
3. **`[Easy]`** What is YAML and why is it used for declarative configuration in Spring Boot and Kubernetes? → [Solution](09_microservices_and_system_design_faq.md#q12-yaml-configuration--declarative-infrastructure)
4. **`[Medium]`** How do Domain-Driven Design (DDD) Bounded Contexts define microservice boundaries? → [Solution](09_microservices_and_system_design_faq.md#q1-microservices-advantages--challenges--ddd-bounded-contexts)
5. **`[Medium]`** How does service discovery and client-side load balancing work in microservices (Eureka vs Consul)? → [Solution](09_microservices_and_system_design_faq.md#q2-service-discovery-load-balancing-eureka-vs-consul--the-api-gateway-pattern)
6. **`[Medium]`** How do microservices communicate? Compare synchronous protocols (REST, gRPC) versus asynchronous messaging (Kafka, RabbitMQ) and their respective architectural trade-offs. → [Solution](09_microservices_and_system_design_faq.md#q3-synchronous-vs-asynchronous-communication--event-driven-architecture)
7. **`[Medium]`** How do you secure inter-service communication across microservices (mTLS and JWT propagation)? → [Solution](09_microservices_and_system_design_faq.md#q4-securing-microservices-communication-mtls--jwt)
8. **`[Medium]`** What is the Circuit Breaker pattern (Resilience4j) and how does it prevent cascading microservice failures? → [Solution](09_microservices_and_system_design_faq.md#q5-circuit-breakers-resilience4j-cascading-failures--production-recovery-mechanisms)
9. **`[Medium]`** What is the difference between Saga Choreography and Saga Orchestration, and how do you choose between them? → [Solution](09_microservices_and_system_design_faq.md#q21-saga-pattern-choreography-vs-orchestration-decision-framework)
10. **`[Medium]`** What is Command Query Responsibility Segregation (CQRS) and what problems does it solve? → [Solution](09_microservices_and_system_design_faq.md#q8-command-query-responsibility-segregation-cqrs-pattern)
11. **`[Medium]`** How do you implement distributed tracing and centralized logging in Spring Boot microservices (Micrometer, OpenTelemetry, Zipkin)? → [Solution](09_microservices_and_system_design_faq.md#q9-distributed-tracing-centralized-logging--semantic-monitoring)
12. **`[Medium]`** What are Consumer-Driven Contracts (CDC with Pact) and how do they ensure API backward compatibility? → [Solution](09_microservices_and_system_design_faq.md#q10-consumer-driven-contracts-cdc--api-backward-compatibility)
13. **`[Medium]`** Explain the CAP theorem and its real-world implications when designing distributed data stores. → [Solution](09_microservices_and_system_design_faq.md#q11-the-cap-theorem--database-scaling-strategies)
14. **`[Medium]`** What are database scaling strategies (Read Replicas, Sharding, Partitioning) for high-scale applications? → [Solution](09_microservices_and_system_design_faq.md#q11-the-cap-theorem--database-scaling-strategies)
15. **`[Hard]`** How do you maintain data consistency across distributed microservices without 2-phase commit (Saga pattern and Transactional Outbox pattern)? → [Solution](09_microservices_and_system_design_faq.md#q6-distributed-transactions-saga--outbox-patterns--eventual-consistency)
16. **`[Hard]`** How do you design an idempotent payment processing endpoint that prevents duplicate credit card charges even under client retry storms? → [Solution](09_microservices_and_system_design_faq.md#q7-designing-end-to-end-idempotency-in-payment-processing)
17. **`[Hard]`** Design a distributed rate limiter in Java. What happens when its distributed database (Redis) goes down? → [Solution](09_microservices_and_system_design_faq.md#q17-designing-a-distributed-rate-limiter-in-java-with-redis-fallback-degradation)
18. **`[Hard]`** Write a generic Circuit Breaker implementation from scratch in Java around an external remote service call. → [Solution](09_microservices_and_system_design_faq.md#q18-custom-generic-circuit-breaker-implementation-in-plain-java)
19. **`[Hard]`** Design a scalable URL shortener system (like bit.ly) handling 10,000 writes/sec and 100,000 reads/sec. → [Solution](09_microservices_and_system_design_faq.md#q13-system-design-scalable-url-shortener-bitly)
20. **`[Hard]`** Design an algorithmic stream processor to calculate Top K elements across distributed worker nodes in real-time. → [Solution](09_microservices_and_system_design_faq.md#q15-distributed-algorithm-finding-top-k-elements-across-distributed-servers)
21. **`[Hard]`** Design a real-time anomaly detection stream algorithm to detect fraudulent transactions over a rolling window. → [Solution](09_microservices_and_system_design_faq.md#q16-streaming-algorithm-real-time-anomaly-detection-over-rolling-windows)
22. **`[Expert]`** Design an end-to-end high-throughput real-time chat architecture supporting millions of concurrent WebSocket connections and cross-node routing. → [Solution](09_microservices_and_system_design_faq.md#q14-system-design-high-throughput-real-time-chat-millions-of-msgssec)
23. **`[Expert]`** How would you design a Java backend system to handle tens of thousands of concurrent requests without server thread pool exhaustion? → [Solution](09_microservices_and_system_design_faq.md#q19-high-throughput-backend-architecture-for-handling-thousands-of-concurrent-requests)
24. **`[Expert]`** Design a highly available payment processing platform with state machines, distributed locking, and zero duplicate transactions. → [Solution](09_microservices_and_system_design_faq.md#q20-high-availability-payment-processing-platform-with-zero-double-charges)
25. **`[Expert]`** A downstream billing service suffers intermittent 30-second timeouts. How do you redesign microservices resilience using timeouts, circuit breakers, retries, and bulkheads? → [Solution](09_microservices_and_system_design_faq.md#q5-circuit-breakers-resilience4j-cascading-failures--production-recovery-mechanisms)
26. **`[Expert]`** A Spring Boot microservices platform experiences cascading timeouts causing all services to crash during a marketing sale. How do you re-architect the system? → [Solution](09_microservices_and_system_design_faq.md#q5-circuit-breakers-resilience4j-cascading-failures--production-recovery-mechanisms)
27. **`[Expert]`** How do you design an event-driven Java system that handles duplicate messages, retries failed events, and maintains reliable processing without losing business transactions? → [Solution](09_microservices_and_system_design_faq.md#q6-distributed-transactions-saga--outbox-patterns--eventual-consistency)
28. **`[Expert]`** How do you build an application so that it continues working seamlessly when one cloud instance crashes (High Availability, stateless design, multi-AZ deployment)? → [Solution](09_microservices_and_system_design_faq.md#q2-service-discovery-load-balancing-eureka-vs-consul--the-api-gateway-pattern)
29. **`[Expert]`** How would you design a distributed cache invalidation architecture using Redis Pub/Sub to maintain cache coherency across 50 application nodes? → [Solution](09_microservices_and_system_design_faq.md#q11-the-cap-theorem--database-scaling-strategies)
30. **`[Expert]`** Design a multi-region active-active distributed architecture for a global banking platform with sub-second latency and zero data loss. → [Solution](09_microservices_and_system_design_faq.md#q11-the-cap-theorem--database-scaling-strategies)
31. **`[Expert]`** An e-commerce flash sale causes connection pool starvation across 20 microservices within 30 seconds of launch. What emergency architectural steps do you take? → [Solution](09_microservices_and_system_design_faq.md#q5-circuit-breakers-resilience4j-cascading-failures--production-recovery-mechanisms)
32. **`[Expert]`** Design a High-Throughput Trailing Stop-Loss Order Execution Engine with sub-millisecond price evaluations and concurrent lock-free order books. → [Solution](09_microservices_and_system_design_faq.md#q22-design-a-high-throughput-trailing-stop-loss-order-execution-engine)
33. **`[Hard]`** How do you isolate slow downstream microservices to prevent cascading thread pool collapse using Circuit Breakers, Timeouts, and Bulkheads? → [Solution](09_microservices_and_system_design_faq.md#q23-isolating-slow-downstream-microservices-cascading-failure-prevention-via-circuit-breakers-timeouts--bulkheads)
34. **`[Medium]`** How do you prevent retry storms during cascading outages using exponential backoff with full jitter and circuit breakers? → [Solution](09_microservices_and_system_design_faq.md#q24-retry-storm-prevention--exponential-backoff-with-jitter)
35. **`[Hard]`** How do you detect and decouple a distributed monolith into independent, loosely coupled microservices? → [Solution](09_microservices_and_system_design_faq.md#q25-detecting--decoupling-a-distributed-monolith)
---

## 10 – Cloud Native, DevOps, Observability & Production Troubleshooting

*Companion Deep-Dive Guide: [`10_cloud_devops_and_observability_faq.md`](10_cloud_devops_and_observability_faq.md) (44 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between a Docker image and a Docker container? → [Solution](10_cloud_devops_and_observability_faq.md#q1-docker-image-vs-container--multi-stage-dockerfile-for-spring-boot)
2. **`[Easy]`** What is the difference between Liveness, Readiness, and Startup probes in Kubernetes? → [Solution](10_cloud_devops_and_observability_faq.md#q2-liveness-vs-readiness-probes-in-kubernetes)
3. **`[Easy]`** What are the common deployment strategies: Blue-Green vs Canary vs Rolling Updates? → [Solution](10_cloud_devops_and_observability_faq.md#q5-deployment-strategies-blue-green-vs-canary-vs-rolling)
4. **`[Easy]`** What is the Azure resource hierarchy (Management Groups, Subscriptions, Resource Groups, Resources)? → [Solution](10_cloud_devops_and_observability_faq.md#q7-azure-resource-hierarchy--resource-groups)
5. **`[Easy]`** What is the difference between Horizontal Scaling (Scale-Out) and Vertical Scaling (Scale-Up)? → [Solution](10_cloud_devops_and_observability_faq.md#q11-horizontal-vs-vertical-scaling-in-azure)
6. **`[Medium]`** How do you write a multi-stage Dockerfile for a Spring Boot application using Eclipse Temurin distroless base images? → [Solution](10_cloud_devops_and_observability_faq.md#q1-docker-image-vs-container--multi-stage-dockerfile-for-spring-boot)
7. **`[Medium]`** How does Kubernetes Horizontal Pod Autoscaler (HPA) and CoreDNS service discovery work for Spring Boot pods? → [Solution](10_cloud_devops_and_observability_faq.md#q3-kubernetes-autoscaling-hpa-vpa-cluster-autoscaler--service-discovery)
8. **`[Medium]`** How do you structure a robust CI/CD pipeline using GitHub Actions / Jenkins with automated quality gates? → [Solution](10_cloud_devops_and_observability_faq.md#q4-structuring-a-robust-cicd-pipeline-github-actions--jenkins)
9. **`[Medium]`** What are the core components of an enterprise production observability stack (Prometheus, Grafana, OpenTelemetry)? → [Solution](10_cloud_devops_and_observability_faq.md#q6-enterprise-production-monitoring--observability-stack)
10. **`[Medium]`** What is the difference between Azure Managed Identities and Service Principals for secure cloud access? → [Solution](10_cloud_devops_and_observability_faq.md#q8-azure-identity-iam-roles-managed-identities--service-principals)
11. **`[Medium]`** What is the difference between Azure Blob Storage and Azure Files, and Service Bus vs Storage Queue? → [Solution](10_cloud_devops_and_observability_faq.md#q9-azure-storage-blob-vs-files--queues-service-bus-vs-storage-queue)
12. **`[Medium]`** What are the differences between Azure Load Balancer (Layer 4) and Azure Application Gateway (Layer 7)? → [Solution](10_cloud_devops_and_observability_faq.md#q10-azure-load-balancer-architecture--types)
13. **`[Medium]`** Explain the full AWS deployment flow — from Docker image creation in CI to ECR push and EKS container rollout. → [Solution](10_cloud_devops_and_observability_faq.md#q15-end-to-end-aws-deployment-flow-ci-build-to-ecr-and-eks-container-rollout)
14. **`[Medium]`** Which AWS services have you integrated in enterprise Java backend engineering (ECS/EKS, RDS, SQS, S3, Secrets Manager)? → [Solution](10_cloud_devops_and_observability_faq.md#q16-core-aws-services-integration-in-enterprise-java-backend-engineering)
15. **`[Medium]`** How do you securely manage database credentials and API secrets in Azure using Azure Key Vault and Secrets Store CSI Driver? → [Solution](10_cloud_devops_and_observability_faq.md#q13-azure-compliance-governance--secret-management)
16. **`[Medium]`** Explain AWS Virtual Private Cloud (VPC), Public vs Private subnets, and NAT Gateway routing for Java applications. → [Solution](10_cloud_devops_and_observability_faq.md#q14-aws-virtual-private-cloud-vpc--network-resource-isolation)
17. **`[Hard]`** How do you achieve Zero-Downtime deployments in Azure Kubernetes Service (AKS) or Amazon EKS with Spring Boot graceful shutdown? → [Solution](10_cloud_devops_and_observability_faq.md#q12-azure-compute-models-vm-vs-app-service-vs-functions-vs-aks--zero-downtime-deployments)
18. **`[Hard]`** How does container memory sizing and cgroups v2 interact with the JVM to prevent Linux OOMKilled errors (-XX:MaxRAMPercentage)? → [Solution](10_cloud_devops_and_observability_faq.md#q17-production-kubernetes-architecture-for-java-microservices-memory-limits--cgroups-v2)
19. **`[Hard]`** Explain the compute trade-offs between Azure VMs (IaaS), App Service (PaaS), Functions (Serverless), and AKS (Containers). → [Solution](10_cloud_devops_and_observability_faq.md#q12-azure-compute-models-vm-vs-app-service-vs-functions-vs-aks--zero-downtime-deployments)
20. **`[Expert]`** Design an enterprise multi-region Kubernetes disaster recovery architecture with automated DNS failover and zero data loss. → [Solution](10_cloud_devops_and_observability_faq.md#q12-azure-compute-models-vm-vs-app-service-vs-functions-vs-aks--zero-downtime-deployments)
21. **`[Easy]`** How do you perform local and remote debugging in Java applications using JDWP (Java Debug Wire Protocol)? → [Solution](10_cloud_devops_and_observability_faq.md#q18-local-and-remote-debugging-via-jdwp--port-distinctions)
22. **`[Easy]`** What is the difference between the application HTTP port (8080) and the remote debug port (5005)? → [Solution](10_cloud_devops_and_observability_faq.md#q18-local-and-remote-debugging-via-jdwp--port-distinctions)
23. **`[Easy]`** Why does increasing logging verbosity to DEBUG in production significantly degrade throughput and increase latency? → [Solution](10_cloud_devops_and_observability_faq.md#q29-why-increasing-log-verbosity-degrades-performance)
24. **`[Easy]`** How do you troubleshoot and fix NoSuchBeanDefinitionException in Spring Boot? → [Solution](10_cloud_devops_and_observability_faq.md#q32-troubleshooting-nosuchbeandefinitionexception-in-spring-boot)
25. **`[Medium]`** Service is slow, but CPU usage is low (10%). What do you check first (Database locks, thread pool exhaustion, I/O wait)? → [Solution](10_cloud_devops_and_observability_faq.md#q19-service-slow-but-cpu-usage-is-low-what-to-check-first)
26. **`[Medium]`** Application runs fine for hours, then suddenly slows down. What are the common causes (Metaspace leak, GC thrashing, connection leaks)? → [Solution](10_cloud_devops_and_observability_faq.md#q20-application-runs-fine-for-hours-then-suddenly-slows-down)
27. **`[Medium]`** High CPU usage (95%) with very low traffic. What investigation steps do you take (Infinite loop, JIT compilation, Stop-The-World GC)? → [Solution](10_cloud_devops_and_observability_faq.md#q21-high-cpu-usage-with-very-low-traffic-investigation-steps)
28. **`[Medium]`** API response time is inconsistent: 90% of requests take 20ms, but 10% take 5000ms. How do you troubleshoot the p99 latency spike? → [Solution](10_cloud_devops_and_observability_faq.md#q22-inconsistent-api-response-time-without-code-changes)
29. **`[Medium]`** How do you isolate heavy asynchronous background jobs from impacting real-time user-facing API latency? → [Solution](10_cloud_devops_and_observability_faq.md#q23-isolating-background-jobs-from-impacting-api-latency)
30. **`[Medium]`** Why does HashMap lookup time degrade from O(1) to O(N) under high load (Hash collision attacks and mutable keys)? → [Solution](10_cloud_devops_and_observability_faq.md#q26-hashmap-lookup-time-degrades-from-o1-to-on)
31. **`[Medium]`** What are Retry Storms, and how do poorly configured retries cause cascading production outages? → [Solution](10_cloud_devops_and_observability_faq.md#q31-retry-storms-how-poorly-configured-retries-cause-cascading-outages)
32. **`[Medium]`** How do you troubleshoot and resolve 504 Gateway Timeout and 503 Service Unavailable errors in production? → [Solution](10_cloud_devops_and_observability_faq.md#q33-troubleshooting-and-resolving-504-gateway-timeout-and-503-service-unavailable)
33. **`[Medium]`** You are handed a Java pull request to review. What production checklist do you follow (Concurrency, N+1 queries, security, observability)? → [Solution](10_cloud_devops_and_observability_faq.md#q36-enterprise-java-pull-request-code-review-checklist)
34. **`[Hard]`** How do you debug rare production deadlocks that cannot be reproduced locally using thread dumps (jstack, top -H)? → [Solution](10_cloud_devops_and_observability_faq.md#q24-debugging-rare-production-deadlocks-that-cannot-be-reproduced-locally)
35. **`[Hard]`** Process memory usage keeps increasing even though heap memory is stable and GC runs fine. How do you investigate off-heap and native memory leaks (NMT)? → [Solution](10_cloud_devops_and_observability_faq.md#q25-memory-usage-keeps-increasing-with-no-obvious-heap-leaks)
36. **`[Hard]`** GC pauses increase significantly after a small code release. How do you analyze GC logs and heap allocations to locate the cause? → [Solution](10_cloud_devops_and_observability_faq.md#q27-gc-pauses-increase-significantly-after-a-small-release)
37. **`[Hard]`** How do you tune JVM Garbage Collectors (G1 vs ZGC) for low-latency vs high-throughput production workloads? → [Solution](10_cloud_devops_and_observability_faq.md#q28-jvm-tuning-throughput-vs-latency-trade-off)
38. **`[Hard]`** Why does scaling out instances from 5 to 20 sometimes worsen overall system performance instead of improving it (Connection pool saturation)? → [Solution](10_cloud_devops_and_observability_faq.md#q30-why-scaling-instances-worsens-overall-system-performance)
39. **`[Hard]`** A database query that takes 20ms in dev suddenly takes 10 seconds in production. What is your exact diagnostic triage runbook? → [Solution](10_cloud_devops_and_observability_faq.md#q34-production-database-query-suddenly-takes-10-seconds-diagnostic-triage-playbook)
40. **`[Hard]`** A production issue causes widespread failures but application logs contain zero exceptions. What diagnostic steps do you take? → [Solution](10_cloud_devops_and_observability_faq.md#q35-production-outage-with-no-exceptions-in-logs-silent-failure-investigation-runbook)
41. **`[Expert]`** If you inherited an unstable Java microservices platform with high latency, frequent outages, and poor observability, what would your first 90-day engineering improvement plan look like? → [Solution](10_cloud_devops_and_observability_faq.md#q37-the-90-day-engineering-improvement-plan-for-unstable-legacy-java-platforms)
42. **`[Expert]`** How would you modernize a 15-year-old monolithic Java enterprise application into microservices using the Strangler Fig pattern without downtime? → [Solution](10_cloud_devops_and_observability_faq.md#q38-modernizing-legacy-java-monoliths-via-the-strangler-fig-pattern)
43. **`[Hard]`** An API in Spring Boot is taking 5–6 seconds to respond. How will you identify whether the issue is in Java code, database, network, or external calls? → [Solution](10_cloud_devops_and_observability_faq.md#q39-spring-boot-api-56s-latency-breakdown-java-code-vs-database-vs-network-vs-external-calls)
44. **`[Medium]`** Explain race conditions with ConcurrentHashMap (check-then-act flaws). How do you resolve them with atomic primitives? → [Solution](10_cloud_devops_and_observability_faq.md#q40-concurrenthashmap-race-conditions-check-then-act-flaws--atomic-resolution)
45. **`[Hard]`** An application has 200 threads running, but throughput is very low. How do you triage context switching, lock contention, and connection pool starvation? → [Solution](10_cloud_devops_and_observability_faq.md#q41-200-threads-running-with-low-throughput-thread-starvation-pool-sizing--lock-contention)
46. **`[Hard]`** How do you debug an OutOfMemoryError in a Spring Boot application using automated heap dumps and Eclipse MAT dominator trees? → [Solution](10_cloud_devops_and_observability_faq.md#q42-spring-boot-outofmemoryerror-diagnostic-playbook-heap-dumps-metaspace--leak-triage)
47. **`[Medium]`** An application is showing high GC activity even when traffic is low. What causes allocation churn and premature tenuring? → [Solution](10_cloud_devops_and_observability_faq.md#q43-high-gc-activity-with-low-production-traffic-allocation-churn--object-lifecycle-triage)
48. **`[Hard]`** A system handles 10 concurrent requests fine, but fails at 1000. How do you find the exact bottleneck across OS limits, Tomcat, and database pools? → [Solution](10_cloud_devops_and_observability_faq.md#q44-concurrency-scalability-collapse-investigating-10-vs-1000-concurrent-requests)

---

## Summary & Interview Engineering Takeaways

1. **Memory & Runtime Mechanics:** Interview scenarios heavily probe HotSpot JVM heap allocation, object layout, GC pauses, and memory leak diagnosis using dominator trees.
2. **Concurrency & Thread Safety:** Technical questions evaluate thread visibility, memory barriers, non-blocking atomics, and carrier thread pinning in Virtual Threads.
3. **Enterprise Spring Resilience:** System design and framework scenarios test connection pool sizing, transaction boundary leaks, and microservice resilience patterns.

---

## References & Further Reading

- [Java Concurrency in Practice (Brian Goetz)](https://jcip.net/)
- [Effective Java (Joshua Bloch)](https://www.oreilly.com/library/view/effective-java-3rd/9780134686097/)
- [Spring Boot Architecture & Reference Documentation](https://spring.io/projects/spring-boot)
