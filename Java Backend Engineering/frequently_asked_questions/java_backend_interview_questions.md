# Java Backend Engineering Interview Questions — Master Preparation Bank

A comprehensive, enterprise-grade bank of real-world Java Backend Engineering interview questions spanning core language mechanics, Collections, modern Java features, multithreading & concurrency, Spring Boot internals, RESTful API architecture, Spring Security, Relational Databases & JPA/Hibernate, distributed messaging with Apache Kafka & RabbitMQ, microservices system design, cloud container deployment, and production troubleshooting.

Every question is categorized by difficulty tier (`[Easy]`, `[Medium]`, `[Hard]`, `[Expert]`), organized progressively according to the recommended study order, and directly linked to its production-grade deep-dive solution.

---

## Master Topic Directory & Difficulty Matrix

| Category | Domain & Focus Area | Questions | Progressive Difficulty Distribution | Companion Deep-Dive Guide |
| :---: | :--- | :---: | :--- | :--- |
| **01** | [Core Java, Object-Oriented Programming & Algorithms](#01-core-java-object-oriented-programming-algorithms) | 45 Questions | Easy: 13 | Medium: 27 | Hard: 4 | Expert: 1 | [`01_core_java_oop_and_algorithms_faq.md`](01_core_java_oop_and_algorithms_faq.md) |
| **02** | [Java Collections Framework, Generics & Data Handling](#02-java-collections-framework-generics-data-handling) | 38 Questions | Easy: 9 | Medium: 25 | Hard: 4 | Expert: 0 | [`02_collections_framework_faq.md`](02_collections_framework_faq.md) |
| **03** | [Java 8+ Modern Features, Lambdas & Functional Streams](#03-java-8-modern-features-lambdas-functional-streams) | 17 Questions | Easy: 6 | Medium: 10 | Hard: 1 | Expert: 0 | [`03_java8_functional_and_modern_features_faq.md`](03_java8_functional_and_modern_features_faq.md) |
| **04** | [Multithreading, Concurrency & Asynchronous Programming](#04-multithreading-concurrency-asynchronous-programming) | 21 Questions | Easy: 3 | Medium: 10 | Hard: 7 | Expert: 1 | [`04_multithreading_concurrency_and_async_faq.md`](04_multithreading_concurrency_and_async_faq.md) |
| **05** | [Spring Framework Core & Spring Boot Architecture](#05-spring-framework-core-spring-boot-architecture) | 22 Questions | Easy: 7 | Medium: 10 | Hard: 4 | Expert: 1 | [`05_spring_framework_and_spring_boot_core_faq.md`](05_spring_framework_and_spring_boot_core_faq.md) |
| **06** | [RESTful Web Services, API Design & Communication Protocols](#06-restful-web-services-api-design-communication-protocols) | 22 Questions | Easy: 8 | Medium: 9 | Hard: 3 | Expert: 2 | [`06_rest_apis_and_web_services_faq.md`](06_rest_apis_and_web_services_faq.md) |
| **07** | [Spring Security, OAuth2, OpenID Connect & JWT Architecture](#07-spring-security-oauth2-openid-connect-jwt-architecture) | 22 Questions | Easy: 5 | Medium: 10 | Hard: 4 | Expert: 3 | [`07_spring_security_and_oauth2_jwt_faq.md`](07_spring_security_and_oauth2_jwt_faq.md) |
| **08** | [Relational Databases, SQL Optimization, JPA & Hibernate ORM](#08-relational-databases-sql-optimization-jpa-hibernate-orm) | 27 Questions | Easy: 7 | Medium: 14 | Hard: 5 | Expert: 1 | [`08_databases_jpa_and_hibernate_faq.md`](08_databases_jpa_and_hibernate_faq.md) |
| **09** | [Distributed Messaging Systems: Apache Kafka & RabbitMQ](#09-distributed-messaging-systems-apache-kafka-rabbitmq) | 11 Questions | Easy: 2 | Medium: 4 | Hard: 4 | Expert: 1 | [`09_messaging_systems_kafka_rabbitmq_faq.md`](09_messaging_systems_kafka_rabbitmq_faq.md) |
| **10** | [Microservices Architecture, Distributed Systems & System Design](#10-microservices-architecture-distributed-systems-system-design) | 31 Questions | Easy: 3 | Medium: 11 | Hard: 7 | Expert: 10 | [`10_microservices_and_system_design_faq.md`](10_microservices_and_system_design_faq.md) |
| **11** | [Cloud Platforms, DevOps, Docker Containers & Kubernetes (K8s)](#11-cloud-platforms-devops-docker-containers-kubernetes-k8s) | 20 Questions | Easy: 5 | Medium: 11 | Hard: 3 | Expert: 1 | [`11_cloud_devops_docker_k8s_azure_faq.md`](11_cloud_devops_docker_k8s_azure_faq.md) |
| **12** | [Production Troubleshooting, Observability, Memory Diagnostics & Real-World Triage](#12-production-troubleshooting-observability-memory-diagnostics-real-world-triage) | 22 Questions | Easy: 4 | Medium: 9 | Hard: 7 | Expert: 2 | [`12_troubleshooting_performance_and_production_faq.md`](12_troubleshooting_performance_and_production_faq.md) |
| **TOTAL** | **Enterprise Java Backend Preparation Bank** | **298 Questions** | **All 12 Disciplines Fully Covered** | **[12 Companion Guides](00_java_faq_study_guide_and_index.md)** |

---

## 01 – Core Java, Object-Oriented Programming & Algorithms

*Companion Deep-Dive Guide: [`01_core_java_oop_and_algorithms_faq.md`](01_core_java_oop_and_algorithms_faq.md) (45 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between the == operator and the equals() method in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q11--q25--vs-equals-and-the-string-pool-mechanism)
2. **`[Easy]`** How does short-circuiting work with logical operators (&&, ||) in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q12-short-circuiting-with-logical-operators)
3. **`[Easy]`** What is the use of constructor in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q13--q14-constructors-and-invoking-static-methods-inside-constructors)
4. **`[Easy]`** Can you invoke a static method inside a constructor in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q13--q14-constructors-and-invoking-static-methods-inside-constructors)
5. **`[Easy]`** What is the purpose of the finally block in Java exception handling? → [Solution](01_core_java_oop_and_algorithms_faq.md#q26--q27-final-finally-and-finalize)
6. **`[Easy]`** What are the differences between final, finally, and finalize() in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q26--q27-final-finally-and-finalize)
7. **`[Easy]`** What is the difference between StringBuffer and StringBuilder? → [Solution](01_core_java_oop_and_algorithms_faq.md#q24-stringbuffer-vs-stringbuilder)
8. **`[Easy]`** Contrast Method Overloading vs Method Overriding, and predict the output of an inheritance snippet involving static methods (Method Hiding vs Dynamic Dispatch). → [Solution](01_core_java_oop_and_algorithms_faq.md#q42-static-method-hiding-vs-dynamic-method-overriding-in-java-inheritance)
9. **`[Easy]`** What is method hiding in Java and how does it differ from overriding? → [Solution](01_core_java_oop_and_algorithms_faq.md#q19--q20-overriding-static-methods--method-hiding)
10. **`[Easy]`** Can you override a static method in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q19--q20-overriding-static-methods--method-hiding)
11. **`[Easy]`** What is an anonymous inner class, and how is it used in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q21-anonymous-inner-classes--scoping)
12. **`[Easy]`** What is a marker interface, and what are its practical use cases? → [Solution](01_core_java_oop_and_algorithms_faq.md#q18-marker-interfaces-and-modern-alternatives)
13. **`[Easy]`** What are text blocks, and where are they useful in modern Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q36-text-blocks-and-multiline-strings)
14. **`[Medium]`** How do you reverse a string in Java without using built-in API functions? → [Solution](01_core_java_oop_and_algorithms_faq.md#q1-reverse-a-string-without-built-ins)
15. **`[Medium]`** How do you convert the first half of a string to lowercase and the second half to uppercase in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q2-convert-first-half-to-lowercase--second-half-to-uppercase)
16. **`[Medium]`** How do you find the first non-repeating character in a string? → [Solution](01_core_java_oop_and_algorithms_faq.md#q3-first-non-repeating-character-in-a-string)
17. **`[Medium]`** How do you find the length of the longest substring without repeating characters? → [Solution](01_core_java_oop_and_algorithms_faq.md#q4-longest-substring-without-repeating-characters)
18. **`[Medium]`** How do you find the longest common prefix among an array of strings? → [Solution](01_core_java_oop_and_algorithms_faq.md#q5-longest-common-prefix-in-an-array-of-strings)
19. **`[Medium]`** How do you remove duplicates from an array in a single iteration? → [Solution](01_core_java_oop_and_algorithms_faq.md#q6-remove-duplicates-from-an-array-in-a-single-iteration)
20. **`[Medium]`** How do you rearrange an integer array so that all even numbers appear before all odd numbers? → [Solution](01_core_java_oop_and_algorithms_faq.md#q7-rearrange-integer-array-even-before-odd)
21. **`[Medium]`** How do you print prime numbers between 1 and 200 with minimal time complexity (Sieve of Eratosthenes)? → [Solution](01_core_java_oop_and_algorithms_faq.md#q8-print-prime-numbers-between-1-and-200-minimal-complexity)
22. **`[Medium]`** How do you reverse a singly linked list iteratively and recursively? → [Solution](01_core_java_oop_and_algorithms_faq.md#q9-reverse-a-singly-linked-list-iterative--recursive)
23. **`[Medium]`** Detect a cycle in a singly linked list without using extra space (Floyd's Tortoise and Hare Algorithm). → [Solution](01_core_java_oop_and_algorithms_faq.md#q41-detect-a-cycle-in-a-singly-linked-list-without-using-extra-space)
24. **`[Medium]`** How do you verify if a path from root to leaf in a binary tree sums to a given target value? → [Solution](01_core_java_oop_and_algorithms_faq.md#q10-path-sum-verification-in-a-binary-tree-root-to-leaf)
25. **`[Medium]`** Given String s1 = '00011111', check whether zeros or ones occur more frequently without using a third variable. → [Solution](01_core_java_oop_and_algorithms_faq.md#q43-character-parity--zero-vs-one-frequency-comparison-without-using-a-third-variable)
26. **`[Medium]`** Given rows of student name and score, return the highest average score, rounded down. → [Solution](01_core_java_oop_and_algorithms_faq.md#q44-highest-average-student-score-rounded-down-from-tabular-data)
27. **`[Medium]`** What are checked exceptions vs unchecked exceptions? What are their advantages, disadvantages, and performance impact? → [Solution](01_core_java_oop_and_algorithms_faq.md#q45-checked-vs-unchecked-exceptions-performance-overhead--controlleradvice-exception-handling)
28. **`[Medium]`** Why is composition preferred over inheritance in object-oriented design? → [Solution](01_core_java_oop_and_algorithms_faq.md#q15-composition-over-inheritance)
29. **`[Medium]`** When should you use an interface versus an abstract class in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q16--q17-interface-vs-abstract-class--static-vs-default-methods)
30. **`[Medium]`** What is the difference between static and default methods in Java interfaces? → [Solution](01_core_java_oop_and_algorithms_faq.md#q16--q17-interface-vs-abstract-class--static-vs-default-methods)
31. **`[Medium]`** How do you create a deeply immutable class in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q22-designing-completely-immutable-classes)
32. **`[Medium]`** What is the difference between shallow copy and deep copy in Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q23-shallow-copy-vs-deep-copy)
33. **`[Medium]`** How does the String Pool work in Java, and why are string literals pooled? → [Solution](01_core_java_oop_and_algorithms_faq.md#q11--q25--vs-equals-and-the-string-pool-mechanism)
34. **`[Medium]`** What causes a ClassCastException, and how can it be prevented defensively? → [Solution](01_core_java_oop_and_algorithms_faq.md#q28-classcastexception-root-causes-and-prevention)
35. **`[Medium]`** What are the different types of class loaders in Java (Bootstrap, Platform/Extension, Application)? → [Solution](01_core_java_oop_and_algorithms_faq.md#q31-classloader-architecture-and-delegation-model)
36. **`[Medium]`** What is the Java Reflection API, and what are its practical use cases and performance costs? → [Solution](01_core_java_oop_and_algorithms_faq.md#q32--q33-java-reflection-api--dynamic-proxies)
37. **`[Medium]`** What is the difference between the Path and File classes in Java NIO vs IO? → [Solution](01_core_java_oop_and_algorithms_faq.md#q34-java-nio-path-vs-classic-io-file)
38. **`[Medium]`** How does pattern matching for switch work in modern Java (Java 17 / 21)? → [Solution](01_core_java_oop_and_algorithms_faq.md#q37-pattern-matching-for-switch)
39. **`[Medium]`** What are the differences between records, standard classes, and sealed classes in modern Java? → [Solution](01_core_java_oop_and_algorithms_faq.md#q38--q39-records-standard-classes--sealed-classes-in-production)
40. **`[Medium]`** Can you explain a scenario where sealed classes were beneficial in enforcing stricter type hierarchies in your project? → [Solution](01_core_java_oop_and_algorithms_faq.md#q38--q39-records-standard-classes--sealed-classes-in-production)
41. **`[Hard]`** Explain memory management in Java (Heap, Stack, Metaspace, GC and Object Headers). → [Solution](01_core_java_oop_and_algorithms_faq.md#q29--q30-jvm-memory-architecture--handling-outofmemoryerror)
42. **`[Hard]`** What are some common causes of OutOfMemoryError (Heap, Metaspace, DirectBuffer) in Java and how do you handle them? → [Solution](01_core_java_oop_and_algorithms_faq.md#q29--q30-jvm-memory-architecture--handling-outofmemoryerror)
43. **`[Hard]`** What is a Proxy class in Java and how is it used for dynamic method invocation and AOP interception? → [Solution](01_core_java_oop_and_algorithms_faq.md#q32--q33-java-reflection-api--dynamic-proxies)
44. **`[Hard]`** How does Java handle tail call optimization (TCO), and why does HotSpot intentionally preserve complete stack frames? → [Solution](01_core_java_oop_and_algorithms_faq.md#q35-tail-call-optimization-in-java)
45. **`[Expert]`** What are virtual threads in Java 21, and how do they differ from platform threads under carrier thread unmounting? → [Solution](01_core_java_oop_and_algorithms_faq.md#q40-virtual-threads-project-loom-vs-platform-threads)

---

## 02 – Java Collections Framework, Generics & Data Handling

*Companion Deep-Dive Guide: [`02_collections_framework_faq.md`](02_collections_framework_faq.md) (38 Questions & Production Solutions)*

1. **`[Easy]`** What is the main difference between List and Set in Java Collections? → [Solution](02_collections_framework_faq.md#q1-list-vs-set-interfaces)
2. **`[Easy]`** What are the key differences between ArrayList and LinkedList? → [Solution](02_collections_framework_faq.md#q2-arraylist-vs-linkedlist)
3. **`[Easy]`** What is the difference between Arrays.asList() and new ArrayList<>()? → [Solution](02_collections_framework_faq.md#q3--q4-arraysaslist-vs-listof-vs-new-arraylist)
4. **`[Easy]`** What are the differences between List.of() and new ArrayList<>() introduced in Java 9+? → [Solution](02_collections_framework_faq.md#q3--q4-arraysaslist-vs-listof-vs-new-arraylist)
5. **`[Easy]`** How do HashSet, LinkedHashSet, and TreeSet differ in terms of ordering and performance? → [Solution](02_collections_framework_faq.md#q5-hashset-vs-linkedhashset-vs-treeset)
6. **`[Easy]`** Can a HashMap store null keys, and at which bucket index are null keys stored? → [Solution](02_collections_framework_faq.md#q9-q10--q11-hashmap-internals-capacity-load-factor-resizing-null-keys--collisions)
7. **`[Easy]`** What are the differences between poll(), peek(), and remove() methods in a Queue? → [Solution](02_collections_framework_faq.md#q20-poll-vs-peek-vs-remove)
8. **`[Easy]`** What are the differences between Iterator and ListIterator in Java? → [Solution](02_collections_framework_faq.md#q21-iterator-vs-listiterator)
9. **`[Easy]`** What is the difference between Comparable and Comparator interfaces? → [Solution](02_collections_framework_faq.md#q25-comparable-vs-comparator)
10. **`[Medium]`** How does a HashSet work internally, and how does it ensure unique elements? → [Solution](02_collections_framework_faq.md#q6-hashset-internal-working--uniqueness-guarantee)
11. **`[Medium]`** What is the contract between hashCode() and equals(), and what happens if it is violated? → [Solution](02_collections_framework_faq.md#q7--q8-the-hashcode-and-equals-contract--custom-keys)
12. **`[Medium]`** Which methods must you override when using a custom object as a Map key? → [Solution](02_collections_framework_faq.md#q7--q8-the-hashcode-and-equals-contract--custom-keys)
13. **`[Medium]`** What are the default initial capacity, load factor, and threshold of a HashMap? → [Solution](02_collections_framework_faq.md#q9-q10--q11-hashmap-internals-capacity-load-factor-resizing-null-keys--collisions)
14. **`[Medium]`** What happens when two keys collide in a HashMap (LinkedList chaining vs Red-Black Tree treeification)? → [Solution](02_collections_framework_faq.md#q9-q10--q11-hashmap-internals-capacity-load-factor-resizing-null-keys--collisions)
15. **`[Medium]`** What are the differences between HashMap, ConcurrentHashMap, and Hashtable? → [Solution](02_collections_framework_faq.md#q12--q13-hashmap-vs-concurrenthashmap-vs-hashtable--concurrency-mechanics)
16. **`[Medium]`** What is the difference between WeakHashMap and HashMap, and how does it prevent memory leaks? → [Solution](02_collections_framework_faq.md#q14-weakhashmap-vs-hashmap)
17. **`[Medium]`** How does a TreeMap maintain sorting of its keys (Red-Black tree)? → [Solution](02_collections_framework_faq.md#q15--q16-treemap-red-black-tree-sorting--null-keys)
18. **`[Medium]`** Can TreeMap have null keys? Why or why not? → [Solution](02_collections_framework_faq.md#q15--q16-treemap-red-black-tree-sorting--null-keys)
19. **`[Medium]`** How do you use EnumSet and EnumMap in Java, and why are they backed by bit vectors? → [Solution](02_collections_framework_faq.md#q17-enumset-and-enummap)
20. **`[Medium]`** What is the key functional and structural difference between Queue and Deque? → [Solution](02_collections_framework_faq.md#q18-queue-vs-deque)
21. **`[Medium]`** How does PriorityQueue work internally in Java (Binary Min-Heap)? → [Solution](02_collections_framework_faq.md#q19-priorityqueue-internal-working)
22. **`[Medium]`** What is the difference between Fail-Fast and Fail-Safe iterators? → [Solution](02_collections_framework_faq.md#q22-q23--q24-fail-fast-vs-fail-safe-concurrentmodificationexception--safe-removal)
23. **`[Medium]`** What causes a ConcurrentModificationException, and how can you safely remove elements during iteration? → [Solution](02_collections_framework_faq.md#q22-q23--q24-fail-fast-vs-fail-safe-concurrentmodificationexception--safe-removal)
24. **`[Medium]`** How do you safely remove elements from a collection while iterating over it? → [Solution](02_collections_framework_faq.md#q22-q23--q24-fail-fast-vs-fail-safe-concurrentmodificationexception--safe-removal)
25. **`[Medium]`** What are stable sorting algorithms and in-place sorting algorithms (TimSort)? → [Solution](02_collections_framework_faq.md#q26-stable-vs-in-place-sorting-algorithms)
26. **`[Medium]`** How do you create an immutable collection in Java (Collections.unmodifiableList vs List.copyOf)? → [Solution](02_collections_framework_faq.md#q27-creating-truly-immutable-collections)
27. **`[Medium]`** How do you filter a list of employees to get those with a salary greater than 10,000? → [Solution](02_collections_framework_faq.md#q29-filter-employees-by-salary--10000)
28. **`[Medium]`** How do you rearrange a list so the minimum element is at index 0 and maximum is at the last index? → [Solution](02_collections_framework_faq.md#q30-rearrange-list-min-at-first-index-max-at-last-index)
29. **`[Medium]`** How do you find the most frequently occurring element in a list? → [Solution](02_collections_framework_faq.md#q31--q33-finding-frequency-of-elements--most-frequent-element)
30. **`[Medium]`** How do you calculate the frequency of each element in a list using Streams? → [Solution](02_collections_framework_faq.md#q31--q33-finding-frequency-of-elements--most-frequent-element)
31. **`[Medium]`** How do you sort a Map by its values in Java using Streams? → [Solution](02_collections_framework_faq.md#q34-sorting-a-map-by-values)
32. **`[Medium]`** Minimum meeting rooms problem using a Greedy approach and Min-Heap (Meeting Rooms II). → [Solution](02_collections_framework_faq.md#q35-meeting-rooms-ii-greedy-interval-scheduling-via-min-heap-priorityqueue)
33. **`[Medium]`** Finding the Nth-largest element using a bounded Min-Heap (PriorityQueue). → [Solution](02_collections_framework_faq.md#q36-finding-the-nth-largest-element-using-a-min-heap-priorityqueue)
34. **`[Medium]`** Can HashMap keys be mutable? Explain hashcode mutation pitfalls and memory leaks. → [Solution](02_collections_framework_faq.md#q37-can-hashmap-keys-be-mutable-key-mutation-pitfalls--memory-leaks)
35. **`[Hard]`** How does ConcurrentHashMap work internally in Java 8+ to achieve thread safety without locking the entire map (CAS + Node Synchronized)? → [Solution](02_collections_framework_faq.md#q12--q13-hashmap-vs-concurrenthashmap-vs-hashtable--concurrency-mechanics)
36. **`[Hard]`** How would you handle large datasets in Java using Collections without exhausting heap memory? → [Solution](02_collections_framework_faq.md#q28-handling-large-datasets-in-memory-using-collections)
37. **`[Hard]`** How do you implement an LRU Cache in Java with O(1) operations using LinkedHashMap? → [Solution](02_collections_framework_faq.md#q32-lru-cache-implementation-with-o1-operations)
38. **`[Hard]`** Explain HashMap rehashing internals, threshold doubling triggers, and bitwise power-of-two index distribution. → [Solution](02_collections_framework_faq.md#q38-hashmap-rehashing-internals-threshold-triggers--bitwise-power-of-two-indexing)

---

## 03 – Java 8+ Modern Features, Lambdas & Functional Streams

*Companion Deep-Dive Guide: [`03_java8_functional_and_modern_features_faq.md`](03_java8_functional_and_modern_features_faq.md) (17 Questions & Production Solutions)*

1. **`[Easy]`** What is a functional interface in Java, and what are some commonly used built-in functional interfaces? → [Solution](03_java8_functional_and_modern_features_faq.md#q1-q2-q3--q4-functional-interfaces-functionalinterface-built-ins--predicate)
2. **`[Easy]`** What is the significance of the @FunctionalInterface annotation in Java? → [Solution](03_java8_functional_and_modern_features_faq.md#q1-q2-q3--q4-functional-interfaces-functionalinterface-built-ins--predicate)
3. **`[Easy]`** What is the role and purpose of the java.util.function package? → [Solution](03_java8_functional_and_modern_features_faq.md#q1-q2-q3--q4-functional-interfaces-functionalinterface-built-ins--predicate)
4. **`[Easy]`** What is a Predicate interface, and how is it used in Java? → [Solution](03_java8_functional_and_modern_features_faq.md#q1-q2-q3--q4-functional-interfaces-functionalinterface-built-ins--predicate)
5. **`[Easy]`** What are method references in Java and how do they relate to lambdas? → [Solution](03_java8_functional_and_modern_features_faq.md#q5-method-references-in-java)
6. **`[Easy]`** What is Optional used for, and how does it help prevent NullPointerExceptions? → [Solution](03_java8_functional_and_modern_features_faq.md#q6-optional-architecture-and-best-practices-for-npe-prevention)
7. **`[Medium]`** What is a Stream in Java 8, and how does it differ from a Collection? → [Solution](03_java8_functional_and_modern_features_faq.md#q7--q8-streams-vs-collections--internal-pipeline-mechanics)
8. **`[Medium]`** How do Java Streams work internally (Pipeline Fusion, Intermediate vs Terminal Operations)? → [Solution](03_java8_functional_and_modern_features_faq.md#q7--q8-streams-vs-collections--internal-pipeline-mechanics)
9. **`[Medium]`** What is the difference between map() and flatMap() in the Java Stream API? → [Solution](03_java8_functional_and_modern_features_faq.md#q9-map-vs-flatmap)
10. **`[Medium]`** How do you filter a list of employee names starting with 'SU' using the Stream API? → [Solution](03_java8_functional_and_modern_features_faq.md#q11--q12-string-filtering--sorting-with-stream-api)
11. **`[Medium]`** How do you find employee names starting with 'a' or 'A' and sort them using Java Streams? → [Solution](03_java8_functional_and_modern_features_faq.md#q11--q12-string-filtering--sorting-with-stream-api)
12. **`[Medium]`** How do you count the number of vowels in a string using Java 8 Streams? → [Solution](03_java8_functional_and_modern_features_faq.md#q13-counting-vowels-in-a-string-using-streams)
13. **`[Medium]`** How do you find the second highest integer in an unsorted list using the Stream API? → [Solution](03_java8_functional_and_modern_features_faq.md#q14-finding-second-highest-integer-in-an-unsorted-list)
14. **`[Medium]`** Write a Java Stream program to find the maximum salary in each department. → [Solution](03_java8_functional_and_modern_features_faq.md#q15-finding-the-highest--maximum-salary-in-each-department-using-java-streams)
15. **`[Medium]`** Group employees into dynamic age ranges like 20-30 and 30-40 generically using Streams. → [Solution](03_java8_functional_and_modern_features_faq.md#q16-group-employees-into-dynamic-age-ranges-generically-using-streams)
16. **`[Medium]`** Find the first unique (non-repeating) character in a String using Stream API. → [Solution](03_java8_functional_and_modern_features_faq.md#q17-find-the-first-non-repeating-unique-character-in-a-string-using-stream-api)
17. **`[Hard]`** How does the Stream API handle parallelism, and why can parallel streams degrade production latency under I/O workloads? → [Solution](03_java8_functional_and_modern_features_faq.md#q10-parallel-streams--forkjoinpool-concurrency)

---

## 04 – Multithreading, Concurrency & Asynchronous Programming

*Companion Deep-Dive Guide: [`04_multithreading_concurrency_and_async_faq.md`](04_multithreading_concurrency_and_async_faq.md) (21 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between Runnable and Callable interfaces in Java? → [Solution](04_multithreading_concurrency_and_async_faq.md#q1--q2-runnable-vs-callable--calling-run-vs-start)
2. **`[Easy]`** What happens when you call run() instead of start() on a Thread? → [Solution](04_multithreading_concurrency_and_async_faq.md#q1--q2-runnable-vs-callable--calling-run-vs-start)
3. **`[Easy]`** Different ways to create threads in Java, with modern examples (Thread, Runnable, Callable, Virtual Threads). → [Solution](04_multithreading_concurrency_and_async_faq.md#q20-modern-thread-creation-strategies-platform-threads-executorservice--java-21-virtual-threads)
4. **`[Medium]`** What is the difference between wait() and sleep() methods in Java multithreading? → [Solution](04_multithreading_concurrency_and_async_faq.md#q3--q4-the-synchronized-monitor--sleep-vs-wait)
5. **`[Medium]`** How does the synchronized monitor work in Java (monitorenter and monitorexit bytecode)? → [Solution](04_multithreading_concurrency_and_async_faq.md#q3--q4-the-synchronized-monitor--sleep-vs-wait)
6. **`[Medium]`** What is the volatile keyword, and how does it guarantee visibility without atomicity? → [Solution](04_multithreading_concurrency_and_async_faq.md#q5--q6-volatile-vs-atomicinteger-vs-synchronized)
7. **`[Medium]`** What is the difference between volatile and Atomic classes such as AtomicInteger and AtomicReference (CAS operations)? → [Solution](04_multithreading_concurrency_and_async_faq.md#q5--q6-volatile-vs-atomicinteger-vs-synchronized)
8. **`[Medium]`** What happens when multiple threads modify the same shared mutable object without synchronization? → [Solution](04_multithreading_concurrency_and_async_faq.md#q19-shared-mutable-object-hazards-race-conditions-memory-visibility--jmm)
9. **`[Medium]`** Difference between synchronized and ReentrantLock. What are the feature trade-offs? → [Solution](04_multithreading_concurrency_and_async_faq.md#q21-synchronized-vs-reentrantlock-feature-matrix--trade-offs)
10. **`[Medium]`** Why might the JVM fail to terminate after the main() method completes (Non-daemon threads)? → [Solution](04_multithreading_concurrency_and_async_faq.md#q14-why-the-jvm-fails-to-terminate-after-main-completes)
11. **`[Medium]`** Explain what happens when multiple threads update shared state (e.g. bank balance) and how to fix race conditions. → [Solution](04_multithreading_concurrency_and_async_faq.md#q8-q15--q16-executorservice-architecture-saturated-queues--silent-failures)
12. **`[Medium]`** What is the difference between Future and CompletableFuture in Java? → [Solution](04_multithreading_concurrency_and_async_faq.md#q9-q10--q11-future-vs-completablefuture-thenapply-vs-thencompose--concurrency-optimization)
13. **`[Medium]`** What is the difference between thenApply() and thenCompose() in CompletableFuture pipelines? → [Solution](04_multithreading_concurrency_and_async_faq.md#q9-q10--q11-future-vs-completablefuture-thenapply-vs-thencompose--concurrency-optimization)
14. **`[Hard]`** Show how two threads can deadlock in Java, how you detect it via thread dumps, and how you prevent it. → [Solution](04_multithreading_concurrency_and_async_faq.md#q7-deadlock-conditions-thread-dumps--prevention)
15. **`[Hard]`** How does ThreadPoolExecutor work internally (corePoolSize, maximumPoolSize, workQueue, and RejectionExecutionHandler)? → [Solution](04_multithreading_concurrency_and_async_faq.md#q12-fixing-inconsistent-shared-state-bank-balance-race-condition)
16. **`[Hard]`** Why do tasks submitted to an ExecutorService queue up unexpectedly even though the maximum pool size is large? → [Solution](04_multithreading_concurrency_and_async_faq.md#q12-fixing-inconsistent-shared-state-bank-balance-race-condition)
17. **`[Hard]`** Why might ExecutorService tasks fail silently without logging errors, and how can this be resolved? → [Solution](04_multithreading_concurrency_and_async_faq.md#q12-fixing-inconsistent-shared-state-bank-balance-race-condition)
18. **`[Hard]`** Why does parallel stream execution degrade production latency when mixing CPU and I/O tasks? → [Solution](04_multithreading_concurrency_and_async_faq.md#q17-why-parallel-streams-degrade-production-latency)
19. **`[Hard]`** How would you optimize an asynchronous pipeline using CompletableFuture with custom thread pools? → [Solution](04_multithreading_concurrency_and_async_faq.md#q9-q10--q11-future-vs-completablefuture-thenapply-vs-thencompose--concurrency-optimization)
20. **`[Hard]`** Call API 1 and API 2 asynchronously, call API 3 synchronously, then wait for 1 and 2. Plain Java implementation. → [Solution](04_multithreading_concurrency_and_async_faq.md#q18-orchestrating-asynchronous-and-synchronous-remote-calls-with-completablefuture)
21. **`[Expert]`** Under heavy traffic, your application spawns thousands of platform threads causing OOM and CPU thrashing. How do you re-architect with Virtual Threads and bounded queues? → [Solution](04_multithreading_concurrency_and_async_faq.md#q8-q15--q16-executorservice-architecture-saturated-queues--silent-failures)

---

## 05 – Spring Framework Core & Spring Boot Architecture

*Companion Deep-Dive Guide: [`05_spring_framework_and_spring_boot_core_faq.md`](05_spring_framework_and_spring_boot_core_faq.md) (22 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between Spring Framework and Spring Boot? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q1-spring-framework-vs-spring-boot)
2. **`[Easy]`** What is the ApplicationContext in Spring and how does it relate to BeanFactory? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q2-what-is-applicationcontext-in-spring)
3. **`[Easy]`** Difference between @Component, @Service, @Repository, and @Controller stereotypes. → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q5--q6-stereotype-annotations-component-service-repository-controller--scanning)
4. **`[Easy]`** What is the difference between @Primary and @Qualifier annotations in Spring? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q7-primary-vs-qualifier)
5. **`[Easy]`** What is the difference between @Bean and @Autowired annotations? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q8-bean-vs-autowired-in-dependency--database-management)
6. **`[Easy]`** What are the different Spring Bean scopes (Singleton, Prototype, Request, Session)? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q10--q11-spring-bean-scopes--switching-via-scope)
7. **`[Easy]`** What is the difference between prototype scope and request scope in Spring web applications? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q10--q11-spring-bean-scopes--switching-via-scope)
8. **`[Medium]`** Explain IoC (Inversion of Control) and Dependency Injection internals and constructor injection best practices. → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q3--q4-dependency-injection-internals--best-practices-in-spring-boot)
9. **`[Medium]`** Why is constructor injection preferred over field injection in production Spring Boot applications? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q3--q4-dependency-injection-internals--best-practices-in-spring-boot)
10. **`[Medium]`** How does Spring component scanning work across packages starting from @SpringBootApplication? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q5--q6-stereotype-annotations-component-service-repository-controller--scanning)
11. **`[Medium]`** What are circular dependencies in Spring beans, why are they an architectural smell, and how are they resolved? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q9-the-complete-spring-bean-lifecycle)
12. **`[Medium]`** What are Spring Profiles and how do you organize environment-specific configurations? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q15-multi-environment-spring-profiles)
13. **`[Medium]`** What is ViewResolver in Spring MVC and why is it bypassed in RESTful architectures? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q16-viewresolver-in-spring-mvc-architecture)
14. **`[Medium]`** Explain Spring Web exception handling annotations (@ExceptionHandler, @ControllerAdvice, @ResponseStatus). → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q17-spring-web-exception-handling-annotations)
15. **`[Medium]`** How do you design a clean modular Spring Boot architecture across API, domain, service, and persistence layers? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q19-architectural-approach-to-developing-a-new-spring-boot-module)
16. **`[Medium]`** Explain the complete Spring Bean Lifecycle pipeline from instantiation to destruction. → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q21-complete-spring-bean-lifecycle-pipeline)
17. **`[Medium]`** What is @EnableAutoConfiguration and how does it work internally with conditional annotations? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q22-how-enableautoconfiguration-works-internally-with-conditional-annotations)
18. **`[Hard]`** How does Spring Boot auto-configuration work internally using @Conditional annotations and spring.factories? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q12--q13-springbootapplication--auto-configuration-internals)
19. **`[Hard]`** How do you create a custom Spring Boot auto-configuration starter module? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q12--q13-springbootapplication--auto-configuration-internals)
20. **`[Hard]`** How do you securely configure and harden Spring Boot Actuator endpoints in production? → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q18-spring-boot-actuator-in-production--endpoint-security)
21. **`[Hard]`** Explain what happens internally when a Spring Boot application starts (SpringApplication.run bootstrapping sequence). → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q20-what-happens-internally-when-a-spring-boot-application-starts-springapplicationrun)
22. **`[Expert]`** Explain how Spring creates CGLIB and JDK dynamic proxies for @Transactional and @Async beans during post-processing. → [Solution](05_spring_framework_and_spring_boot_core_faq.md#q14-property-injection-via-value-vs-configurationproperties)

---

## 06 – RESTful Web Services, API Design & Communication Protocols

*Companion Deep-Dive Guide: [`06_rest_apis_and_web_services_faq.md`](06_rest_apis_and_web_services_faq.md) (22 Questions & Production Solutions)*

1. **`[Easy]`** Is REST architecture stateless or stateful, and why is statelessness essential for horizontal scalability? → [Solution](06_rest_apis_and_web_services_faq.md#q1-is-rest-architecture-stateless-or-stateful-and-why)
2. **`[Easy]`** What is the difference between @Controller and @RestController in Spring Boot? → [Solution](06_rest_apis_and_web_services_faq.md#q2--q3-controller-vs-restcontroller--http-request-mapping-annotations)
3. **`[Easy]`** What are the common HTTP request mapping annotations in Spring (@GetMapping, @PostMapping, etc.)? → [Solution](06_rest_apis_and_web_services_faq.md#q2--q3-controller-vs-restcontroller--http-request-mapping-annotations)
4. **`[Easy]`** Difference between @RequestParam, @PathVariable, and @RequestBody in Spring MVC. → [Solution](06_rest_apis_and_web_services_faq.md#q4-parameter-binding-requestparam-vs-pathvariable-vs-requestbody)
5. **`[Easy]`** What is the difference between PUT and PATCH HTTP methods? → [Solution](06_rest_apis_and_web_services_faq.md#q5--q6-put-vs-patch--designing-idempotent-api-operations)
6. **`[Easy]`** Comprehensive HTTP status codes taxonomy: When do you return 200, 201, 202, 204, 400, 401, 403, 404, 409, 422, 500, 503? → [Solution](06_rest_apis_and_web_services_faq.md#q7-comprehensive-http-status-codes-taxonomy)
7. **`[Easy]`** What headers do you typically pass in an outbound RestTemplate or WebClient request in enterprise systems? → [Solution](06_rest_apis_and_web_services_faq.md#q20-standard-enterprise-headers-in-resttemplate--webclient-outbound-requests)
8. **`[Easy]`** What is the difference between @ControllerAdvice and @RestControllerAdvice? → [Solution](06_rest_apis_and_web_services_faq.md#q22-controlleradvice-vs-restcontrolleradvice--rfc-7807-problemdetail)
9. **`[Medium]`** How do you design idempotent operations in RESTful APIs using unique Idempotency Keys? → [Solution](06_rest_apis_and_web_services_faq.md#q5--q6-put-vs-patch--designing-idempotent-api-operations)
10. **`[Medium]`** How do you implement global exception handling in Spring Boot REST APIs using @RestControllerAdvice and ProblemDetail? → [Solution](06_rest_apis_and_web_services_faq.md#q8--q11-graceful-error-handling--global-exception-management-via-restcontrolleradvice)
11. **`[Medium]`** How do you perform payload validation in Spring Boot using Hibernate Validator (@Valid, @NotNull, @Size)? → [Solution](06_rest_apis_and_web_services_faq.md#q9--q10-the-dto-pattern--request-flow-across-controller-service-and-repository)
12. **`[Medium]`** How do you handle file uploads in Spring Boot using MultipartFile securely? → [Solution](06_rest_apis_and_web_services_faq.md#q9--q10-the-dto-pattern--request-flow-across-controller-service-and-repository)
13. **`[Medium]`** How do you enable and configure Cross-Origin Resource Sharing (CORS) in Spring Boot? → [Solution](06_rest_apis_and_web_services_faq.md#q8--q11-graceful-error-handling--global-exception-management-via-restcontrolleradvice)
14. **`[Medium]`** What are the differences between RestTemplate, WebClient, and declarative OpenFeign in Spring Boot? → [Solution](06_rest_apis_and_web_services_faq.md#q12-http-clients-resttemplate-vs-webclient-vs-feignclient)
15. **`[Medium]`** How do you implement client-side load balancing and circuit breaking when using OpenFeign? → [Solution](06_rest_apis_and_web_services_faq.md#q13-api-versioning-strategies-in-production)
16. **`[Medium]`** How would you design a REST API endpoint to fetch the last N orders for a customer using Spring Boot and Spring Data JPA? → [Solution](06_rest_apis_and_web_services_faq.md#q14-designing-a-rest-endpoint-for-last-n-orders-spring-boot--spring-data-jpa)
17. **`[Medium]`** What are the standard RESTful API versioning strategies in production (URI path vs Query param vs Header vs Media Type)? → [Solution](06_rest_apis_and_web_services_faq.md#q21-restful-api-versioning-strategies-in-production)
18. **`[Hard]`** How do you secure REST APIs using Spring Security and JWT tokens? → [Solution](06_rest_apis_and_web_services_faq.md#q15-how-spring-boot-handles-high-concurrency-requests)
19. **`[Hard]`** What are the architectural trade-offs between REST, gRPC, and GraphQL for enterprise systems? → [Solution](06_rest_apis_and_web_services_faq.md#q18-rest-vs-grpc-vs-graphql-architectural-comparison)
20. **`[Hard]`** How do you design and test idempotent payment webhook ingestion endpoints? → [Solution](06_rest_apis_and_web_services_faq.md#q19-handling-inconsistent-external-api-calls-nulls--failures-gracefully)
21. **`[Expert]`** How would you design a high-throughput streaming REST API with Server-Sent Events (SSE) and reactive WebFlux backpressure? → [Solution](06_rest_apis_and_web_services_faq.md#q16-rate-limiting--throttling-in-spring-boot-apis)
22. **`[Expert]`** Design an enterprise API Gateway rate limiting architecture using Redis Token Bucket with sub-millisecond overhead. → [Solution](06_rest_apis_and_web_services_faq.md#q17-configuring-spring-security-for-jwt-on-a-subset-of-endpoints)

---

## 07 – Spring Security, OAuth2, OpenID Connect & JWT Architecture

*Companion Deep-Dive Guide: [`07_spring_security_and_oauth2_jwt_faq.md`](07_spring_security_and_oauth2_jwt_faq.md) (22 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between Authentication and Authorization? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q1-authentication-vs-authorization)
2. **`[Easy]`** What is the difference between BCryptPasswordEncoder and plain text password hashing? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q8-bcryptpasswordencoder-vs-plain-text-passwords)
3. **`[Easy]`** What are the differences between @PreAuthorize, @Secured, and @RolesAllowed in method security? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q9--q10-method-level-security-preauthorize-vs-secured-vs-rolesallowed)
4. **`[Easy]`** What is CSRF and why is it disabled for stateless RESTful APIs? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q11-spring-security-csrf-protection-mechanics)
5. **`[Easy]`** What is the difference between OAuth2 and Basic Authentication? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q18-oauth2-vs-basic-authentication)
6. **`[Medium]`** How does Spring Security's SecurityFilterChain execute internally as a chain of servlet filters? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q2-q3-q4--q5-spring-security-internal-architecture-filter-chain--authenticationmanager)
7. **`[Medium]`** What is the role of AuthenticationManager, AuthenticationProvider, and SecurityContextHolder? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q2-q3-q4--q5-spring-security-internal-architecture-filter-chain--authenticationmanager)
8. **`[Medium]`** How do you implement a custom UserDetailsService and AuthenticationProvider in Spring Security? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q6--q7-userdetailsservice--custom-authenticationprovider)
9. **`[Medium]`** What is SpEL (Spring Expression Language) and how is it used in @PreAuthorize("hasRole('ADMIN')")? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q9--q10-method-level-security-preauthorize-vs-secured-vs-rolesallowed)
10. **`[Medium]`** What is CORS and why must it be configured before Spring Security filter evaluation? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q12-remember-me-authentication-architecture)
11. **`[Medium]`** Explain the structure of a JSON Web Token (Header, Payload, Signature) and how RS256 asymmetric signing works. → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q13-why-use-jwt-tokens-instead-of-stateful-sessions)
12. **`[Medium]`** How do you securely store JWT tokens on the client to protect against XSS and CSRF (HttpOnly cookie)? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q15-client-side-jwt-storage--early-token-revocation)
13. **`[Medium]`** What happens if a JWT token expires and how do you implement short-lived access tokens with Refresh Tokens? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q16-expired-tokens-refresh-token-rotation--concurrency)
14. **`[Medium]`** What are common JWT security vulnerabilities (alg: none, weak secret keys) and how do you mitigate them? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q17-jwt-security-risks--mitigations)
15. **`[Medium]`** Explain the OAuth2 Client Credentials Grant and when to use it for backend microservice-to-microservice communication. → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q20-oauth2-client-credentials-grant-for-microservices)
16. **`[Hard]`** Explain the OAuth2 Authorization Code Flow with PKCE (Proof Key for Code Exchange) for mobile and single-page applications. → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q19-oauth2-authorization-code-flow-with-pkce)
17. **`[Hard]`** How do you securely propagate and validate JWT tokens across distributed microservices using Feign and JWKS? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q21-cross-service-jwt-propagation--verification-in-distributed-microservices)
18. **`[Hard]`** How do you implement distributed JWT revocation before expiration using a Redis token blacklist? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q15-client-side-jwt-storage--early-token-revocation)
19. **`[Hard]`** How does refresh token rotation with reuse detection prevent token theft and trigger blast-radius invalidation? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q22-refresh-token-reuse-detection--blast-radius-revocation-workflows)
20. **`[Expert]`** Design an enterprise single-sign-on (SSO) architecture supporting multi-tenant OAuth2 and OpenID Connect with Keycloak and Azure AD. → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q2-q3-q4--q5-spring-security-internal-architecture-filter-chain--authenticationmanager)
21. **`[Expert]`** How would you design a zero-trust mutual TLS (mTLS) and token-exchange microservices security fabric? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q20-oauth2-client-credentials-grant-for-microservices)
22. **`[Expert]`** A security penetration test discovers that user permissions updated in the database take 15 minutes to reflect in JWT tokens. How do you resolve this without centralized session lookups? → [Solution](07_spring_security_and_oauth2_jwt_faq.md#q15-client-side-jwt-storage--early-token-revocation)

---

## 08 – Relational Databases, SQL Optimization, JPA & Hibernate ORM

*Companion Deep-Dive Guide: [`08_databases_jpa_and_hibernate_faq.md`](08_databases_jpa_and_hibernate_faq.md) (27 Questions & Production Solutions)*

1. **`[Easy]`** What are the ACID properties in relational database management systems? → [Solution](08_databases_jpa_and_hibernate_faq.md#q1-acid-properties--database-transaction-isolation-levels)
2. **`[Easy]`** What is the difference between INNER JOIN, LEFT JOIN, RIGHT JOIN, and FULL OUTER JOIN? → [Solution](08_databases_jpa_and_hibernate_faq.md#q2-sql-joins-inner-left-right-and-full-outer)
3. **`[Easy]`** Write an SQL query to fetch the second-highest salary from an Employee table with strict NULL handling. → [Solution](08_databases_jpa_and_hibernate_faq.md#q3--q4-practical-sql-queries-second-highest-salary--department-wise-averages)
4. **`[Easy]`** Write an SQL query to calculate department-wise average salaries and filter departments with average > 50,000. → [Solution](08_databases_jpa_and_hibernate_faq.md#q3--q4-practical-sql-queries-second-highest-salary--department-wise-averages)
5. **`[Easy]`** What is the difference between Primary Key and Unique Key? → [Solution](08_databases_jpa_and_hibernate_faq.md#q1-acid-properties--database-transaction-isolation-levels)
6. **`[Easy]`** What is the difference between DELETE and TRUNCATE statements in SQL? → [Solution](08_databases_jpa_and_hibernate_faq.md#q1-acid-properties--database-transaction-isolation-levels)
7. **`[Easy]`** What are the differences between JpaRepository, CrudRepository, and PagingAndSortingRepository in Spring Data? → [Solution](08_databases_jpa_and_hibernate_faq.md#q26-jparepository-vs-crudrepository-vs-pagingandsortingrepository)
8. **`[Medium]`** Explain database transaction isolation levels (READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE) and anomalies. → [Solution](08_databases_jpa_and_hibernate_faq.md#q1-acid-properties--database-transaction-isolation-levels)
9. **`[Medium]`** What is the difference between Clustered Index and Non-Clustered Index in relational databases? → [Solution](08_databases_jpa_and_hibernate_faq.md#q5--q6-clustered-vs-non-clustered-indexes--slow-query-optimization)
10. **`[Medium]`** How do you identify and optimize slow SQL queries using EXPLAIN execution plans and indexes? → [Solution](08_databases_jpa_and_hibernate_faq.md#q5--q6-clustered-vs-non-clustered-indexes--slow-query-optimization)
11. **`[Medium]`** How do you invoke stored procedures and handle result sets in Java using JDBC CallableStatement? → [Solution](08_databases_jpa_and_hibernate_faq.md#q7-invoking-stored-procedures-via-jdbc)
12. **`[Medium]`** How do you configure multiple DataSources (e.g. Primary and Secondary databases) in a Spring Boot application? → [Solution](08_databases_jpa_and_hibernate_faq.md#q8-connecting-multiple-datasources-in-spring-boot)
13. **`[Medium]`** Explain Hibernate Entity Lifecycle states: Transient, Persistent (Managed), Detached, and Removed. → [Solution](08_databases_jpa_and_hibernate_faq.md#q9-jpa-primary-key-annotations-id--generatedvalue)
14. **`[Medium]`** What is the difference between FetchType.LAZY and FetchType.EAGER in JPA entities, and why is EAGER considered an anti-pattern? → [Solution](08_databases_jpa_and_hibernate_faq.md#q10-hibernate-entity-lifecycle-states)
15. **`[Medium]`** What is the difference between First-Level Cache (Session) and Second-Level Cache (SessionFactory/Ehcache/Redis) in Hibernate? → [Solution](08_databases_jpa_and_hibernate_faq.md#q12-q13--q14-lazy-vs-eager-lazyinitializationexception--the-n1-select-problem)
16. **`[Medium]`** How does Spring @Transactional work internally using Spring AOP dynamic proxies, and why does self-invocation fail? → [Solution](08_databases_jpa_and_hibernate_faq.md#q12-q13--q14-lazy-vs-eager-lazyinitializationexception--the-n1-select-problem)
17. **`[Medium]`** What are the common transaction propagation levels in Spring (REQUIRED, REQUIRES_NEW, SUPPORTS, MANDATORY)? → [Solution](08_databases_jpa_and_hibernate_faq.md#q18-spring-transactional-propagation-levels--pitfalls)
18. **`[Medium]`** What is the difference between Optimistic Locking (@Version) and Pessimistic Locking (PESSIMISTIC_WRITE)? → [Solution](08_databases_jpa_and_hibernate_faq.md#q19--q20-optimistic-vs-pessimistic-locking--the-train-seat-booking-race-condition)
19. **`[Medium]`** How do you implement pagination and sorting efficiently in Spring Data JPA using Pageable and Slice? → [Solution](08_databases_jpa_and_hibernate_faq.md#q21-pagination-and-sorting-in-spring-data-jpa)
20. **`[Medium]`** How is rollback managed for a series of SQL statements across transaction boundaries and savepoints? → [Solution](08_databases_jpa_and_hibernate_faq.md#q25-managing-transaction-rollbacks-across-a-series-of-sql-statements)
21. **`[Medium]`** Write an SQL query to delete duplicate rows from a table while keeping one copy. → [Solution](08_databases_jpa_and_hibernate_faq.md#q27-deleting-duplicate-rows-while-retaining-exactly-one-copy-in-sql)
22. **`[Hard]`** What is the N+1 select problem in ORM frameworks, and what are the 3 production ways to resolve it (JOIN FETCH, EntityGraph, BatchSize)? → [Solution](08_databases_jpa_and_hibernate_faq.md#q11-criteria-api-vs-jpql)
23. **`[Hard]`** How do you resolve the train seat or hotel room booking race condition where two users attempt to reserve the same seat simultaneously? → [Solution](08_databases_jpa_and_hibernate_faq.md#q19--q20-optimistic-vs-pessimistic-locking--the-train-seat-booking-race-condition)
24. **`[Hard]`** How will you make sure multiple instances of a cron job worker don't update the same row (ShedLock vs SELECT FOR UPDATE SKIP LOCKED)? → [Solution](08_databases_jpa_and_hibernate_faq.md#q23-multi-instance-cron-worker-row-concurrency-shedlock-vs-select-for-update-skip-locked)
25. **`[Hard]`** Two concurrent transactions occasionally overwrite each other's updates. How do you diagnose the Lost Update anomaly and choose the right concurrency strategy? → [Solution](08_databases_jpa_and_hibernate_faq.md#q24-lost-update-anomaly-optimistic-vs-pessimistic-locking-strategy-selection)
26. **`[Hard]`** How does Hibernate dirty checking work internally, and why does marking transactions readOnly = true yield major memory and CPU gains? → [Solution](08_databases_jpa_and_hibernate_faq.md#q12-q13--q14-lazy-vs-eager-lazyinitializationexception--the-n1-select-problem)
27. **`[Expert]`** How would you design and implement a Multi-Tenant JPA persistence architecture using Schema-Per-Tenant or Database-Per-Tenant with dynamic routing? → [Solution](08_databases_jpa_and_hibernate_faq.md#q22-multi-tenancy-architecture-in-jpa)

---

## 09 – Distributed Messaging Systems: Apache Kafka & RabbitMQ

*Companion Deep-Dive Guide: [`09_messaging_systems_kafka_rabbitmq_faq.md`](09_messaging_systems_kafka_rabbitmq_faq.md) (11 Questions & Production Solutions)*

1. **`[Easy]`** What is a message broker service and why is it used in enterprise Java architectures? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q1-message-broker-services-in-enterprise-java-architecture)
2. **`[Easy]`** What is an Exchange in AMQP / RabbitMQ and what are the different exchange types (Direct, Fanout, Topic, Headers)? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q2-what-is-an-exchange-in-amqp--rabbitmq)
3. **`[Medium]`** What is the difference between Apache Kafka and RabbitMQ? When would you choose each for an enterprise architecture? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q3-kafka-vs-rabbitmq-core-differences--selection-criteria)
4. **`[Medium]`** How do Kafka partitions enable horizontal scalability and what is a consumer group? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q4--q5-kafka-architecture-brokers-partitions-consumer-groups--scalability)
5. **`[Medium]`** How do you integrate Apache Kafka with Spring Boot using KafkaTemplate and @KafkaListener? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q6-integrating-apache-kafka-with-spring-boot)
6. **`[Medium]`** How do consumer groups, partition rebalancing (Cooperative Sticky), and offset commits (auto vs manual) work during consumer failure? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q11-consumer-groups-partition-rebalancing--manual-offset-commit-strategies)
7. **`[Hard]`** How do you implement Dead Letter Queues (DLQ) and retry policies in Spring Kafka using @RetryableTopic? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q7--q8-dead-letter-queues-dlq-retry-policies--mitigating-slow-consumers)
8. **`[Hard]`** What causes slow consumers and heartbeat timeouts in Kafka (max.poll.interval.ms exceeded), and how do you mitigate partition rebalances? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q7--q8-dead-letter-queues-dlq-retry-policies--mitigating-slow-consumers)
9. **`[Hard]`** How does Kafka interact with databases using Change Data Capture (Debezium), Kafka Connect, and the Transactional Outbox pattern? → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q9-how-kafka-interacts-with-databases-cdc-debezium-kafka-connect--transactional-outbox)
10. **`[Hard]`** Explain Kafka Schema Registry architecture, Avro binary serialization, and schema evolution compatibility modes (BACKWARD, FORWARD, FULL). → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q10-kafka-schema-registry-architecture-avro-serialization--schema-evolution)
11. **`[Expert]`** Design an end-to-end exactly-once event streaming pipeline in Java: idempotent producer, Kafka transactions, and transactional consumer-database writes. → [Solution](09_messaging_systems_kafka_rabbitmq_faq.md#q4--q5-kafka-architecture-brokers-partitions-consumer-groups--scalability)

---

## 10 – Microservices Architecture, Distributed Systems & System Design

*Companion Deep-Dive Guide: [`10_microservices_and_system_design_faq.md`](10_microservices_and_system_design_faq.md) (31 Questions & Production Solutions)*

1. **`[Easy]`** What are microservices and what are their primary advantages and challenges over a monolith? → [Solution](10_microservices_and_system_design_faq.md#q1--q2-microservices-advantages--challenges--ddd-bounded-contexts)
2. **`[Easy]`** What is the role of an API Gateway in microservices architecture? → [Solution](10_microservices_and_system_design_faq.md#q3-q4--q5-service-discovery-load-balancing-eureka-vs-consul--the-api-gateway-pattern)
3. **`[Easy]`** What is YAML and why is it used for declarative configuration in Spring Boot and Kubernetes? → [Solution](10_microservices_and_system_design_faq.md#q22-yaml-configuration--declarative-infrastructure)
4. **`[Medium]`** How do Domain-Driven Design (DDD) Bounded Contexts define microservice boundaries? → [Solution](10_microservices_and_system_design_faq.md#q1--q2-microservices-advantages--challenges--ddd-bounded-contexts)
5. **`[Medium]`** How does service discovery and client-side load balancing work in microservices (Eureka vs Consul)? → [Solution](10_microservices_and_system_design_faq.md#q3-q4--q5-service-discovery-load-balancing-eureka-vs-consul--the-api-gateway-pattern)
6. **`[Medium]`** How do microservices communicate? Compare synchronous protocols (REST, gRPC) versus asynchronous messaging (Kafka, RabbitMQ) and their respective architectural trade-offs. → [Solution](10_microservices_and_system_design_faq.md#q6--q15-synchronous-vs-asynchronous-communication--event-driven-architecture)
7. **`[Medium]`** How do you secure inter-service communication across microservices (mTLS and JWT propagation)? → [Solution](10_microservices_and_system_design_faq.md#q7-securing-microservices-communication-mtls--jwt)
8. **`[Medium]`** What is the Circuit Breaker pattern (Resilience4j) and how does it prevent cascading microservice failures? → [Solution](10_microservices_and_system_design_faq.md#q8-q9--q10-circuit-breakers-resilience4j-cascading-failures--production-recovery-mechanisms)
9. **`[Medium]`** What is the difference between Saga Choreography and Saga Orchestration, and how do you choose between them? → [Solution](10_microservices_and_system_design_faq.md#q31-saga-pattern-choreography-vs-orchestration-decision-framework)
10. **`[Medium]`** What is Command Query Responsibility Segregation (CQRS) and what problems does it solve? → [Solution](10_microservices_and_system_design_faq.md#q14-command-query-responsibility-segregation-cqrs-pattern)
11. **`[Medium]`** How do you implement distributed tracing and centralized logging in Spring Boot microservices (Micrometer, OpenTelemetry, Zipkin)? → [Solution](10_microservices_and_system_design_faq.md#q16--q17-distributed-tracing-centralized-logging--semantic-monitoring)
12. **`[Medium]`** What are Consumer-Driven Contracts (CDC with Pact) and how do they ensure API backward compatibility? → [Solution](10_microservices_and_system_design_faq.md#q18--q19-consumer-driven-contracts-cdc--api-backward-compatibility)
13. **`[Medium]`** Explain the CAP theorem and its real-world implications when designing distributed data stores. → [Solution](10_microservices_and_system_design_faq.md#q20--q21-the-cap-theorem--database-scaling-strategies)
14. **`[Medium]`** What are database scaling strategies (Read Replicas, Sharding, Partitioning) for high-scale applications? → [Solution](10_microservices_and_system_design_faq.md#q20--q21-the-cap-theorem--database-scaling-strategies)
15. **`[Hard]`** How do you maintain data consistency across distributed microservices without 2-phase commit (Saga pattern and Transactional Outbox pattern)? → [Solution](10_microservices_and_system_design_faq.md#q11--q12-distributed-transactions-saga--outbox-patterns--eventual-consistency)
16. **`[Hard]`** How do you design an idempotent payment processing endpoint that prevents duplicate credit card charges even under client retry storms? → [Solution](10_microservices_and_system_design_faq.md#q13-designing-end-to-end-idempotency-in-payment-processing)
17. **`[Hard]`** Design a distributed rate limiter in Java. What happens when its distributed database (Redis) goes down? → [Solution](10_microservices_and_system_design_faq.md#q27-designing-a-distributed-rate-limiter-in-java-with-redis-fallback-degradation)
18. **`[Hard]`** Write a generic Circuit Breaker implementation from scratch in Java around an external remote service call. → [Solution](10_microservices_and_system_design_faq.md#q28-custom-generic-circuit-breaker-implementation-in-plain-java)
19. **`[Hard]`** Design a scalable URL shortener system (like bit.ly) handling 10,000 writes/sec and 100,000 reads/sec. → [Solution](10_microservices_and_system_design_faq.md#q23-system-design-scalable-url-shortener-bitly)
20. **`[Hard]`** Design an algorithmic stream processor to calculate Top K elements across distributed worker nodes in real-time. → [Solution](10_microservices_and_system_design_faq.md#q25-distributed-algorithm-finding-top-k-elements-across-distributed-servers)
21. **`[Hard]`** Design a real-time anomaly detection stream algorithm to detect fraudulent transactions over a rolling window. → [Solution](10_microservices_and_system_design_faq.md#q26-streaming-algorithm-real-time-anomaly-detection-over-rolling-windows)
22. **`[Expert]`** Design an end-to-end high-throughput real-time chat architecture supporting millions of concurrent WebSocket connections and cross-node routing. → [Solution](10_microservices_and_system_design_faq.md#q24-system-design-high-throughput-real-time-chat-millions-of-msgssec)
23. **`[Expert]`** How would you design a Java backend system to handle tens of thousands of concurrent requests without server thread pool exhaustion? → [Solution](10_microservices_and_system_design_faq.md#q29-high-throughput-backend-architecture-for-handling-thousands-of-concurrent-requests)
24. **`[Expert]`** Design a highly available payment processing platform with state machines, distributed locking, and zero duplicate transactions. → [Solution](10_microservices_and_system_design_faq.md#q30-high-availability-payment-processing-platform-with-zero-double-charges)
25. **`[Expert]`** A downstream billing service suffers intermittent 30-second timeouts. How do you redesign microservices resilience using timeouts, circuit breakers, retries, and bulkheads? → [Solution](10_microservices_and_system_design_faq.md#q8-q9--q10-circuit-breakers-resilience4j-cascading-failures--production-recovery-mechanisms)
26. **`[Expert]`** A Spring Boot microservices platform experiences cascading timeouts causing all services to crash during a marketing sale. How do you re-architect the system? → [Solution](10_microservices_and_system_design_faq.md#q8-q9--q10-circuit-breakers-resilience4j-cascading-failures--production-recovery-mechanisms)
27. **`[Expert]`** How do you design an event-driven Java system that handles duplicate messages, retries failed events, and maintains reliable processing without losing business transactions? → [Solution](10_microservices_and_system_design_faq.md#q11--q12-distributed-transactions-saga--outbox-patterns--eventual-consistency)
28. **`[Expert]`** How do you build an application so that it continues working seamlessly when one cloud instance crashes (High Availability, stateless design, multi-AZ deployment)? → [Solution](10_microservices_and_system_design_faq.md#q3-q4--q5-service-discovery-load-balancing-eureka-vs-consul--the-api-gateway-pattern)
29. **`[Expert]`** How would you design a distributed cache invalidation architecture using Redis Pub/Sub to maintain cache coherency across 50 application nodes? → [Solution](10_microservices_and_system_design_faq.md#q20--q21-the-cap-theorem--database-scaling-strategies)
30. **`[Expert]`** Design a multi-region active-active distributed architecture for a global banking platform with sub-second latency and zero data loss. → [Solution](10_microservices_and_system_design_faq.md#q20--q21-the-cap-theorem--database-scaling-strategies)
31. **`[Expert]`** An e-commerce flash sale causes connection pool starvation across 20 microservices within 30 seconds of launch. What emergency architectural steps do you take? → [Solution](10_microservices_and_system_design_faq.md#q8-q9--q10-circuit-breakers-resilience4j-cascading-failures--production-recovery-mechanisms)

---

## 11 – Cloud Platforms, DevOps, Docker Containers & Kubernetes (K8s)

*Companion Deep-Dive Guide: [`11_cloud_devops_docker_k8s_azure_faq.md`](11_cloud_devops_docker_k8s_azure_faq.md) (20 Questions & Production Solutions)*

1. **`[Easy]`** What is the difference between a Docker image and a Docker container? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q1--q2-docker-image-vs-container--multi-stage-dockerfile-for-spring-boot)
2. **`[Easy]`** What is the difference between Liveness, Readiness, and Startup probes in Kubernetes? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q3-liveness-vs-readiness-probes-in-kubernetes)
3. **`[Easy]`** What are the common deployment strategies: Blue-Green vs Canary vs Rolling Updates? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q6-deployment-strategies-blue-green-vs-canary-vs-rolling)
4. **`[Easy]`** What is the Azure resource hierarchy (Management Groups, Subscriptions, Resource Groups, Resources)? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q8-azure-resource-hierarchy--resource-groups)
5. **`[Easy]`** What is the difference between Horizontal Scaling (Scale-Out) and Vertical Scaling (Scale-Up)? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q12-horizontal-vs-vertical-scaling-in-azure)
6. **`[Medium]`** How do you write a multi-stage Dockerfile for a Spring Boot application using Eclipse Temurin distroless base images? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q1--q2-docker-image-vs-container--multi-stage-dockerfile-for-spring-boot)
7. **`[Medium]`** How does Kubernetes Horizontal Pod Autoscaler (HPA) and CoreDNS service discovery work for Spring Boot pods? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q4-kubernetes-autoscaling-hpa-vpa-cluster-autoscaler--service-discovery)
8. **`[Medium]`** How do you structure a robust CI/CD pipeline using GitHub Actions / Jenkins with automated quality gates? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q5-structuring-a-robust-cicd-pipeline-github-actions--jenkins)
9. **`[Medium]`** What are the core components of an enterprise production observability stack (Prometheus, Grafana, OpenTelemetry)? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q7-enterprise-production-monitoring--observability-stack)
10. **`[Medium]`** What is the difference between Azure Managed Identities and Service Principals for secure cloud access? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q9-azure-identity-iam-roles-managed-identities--service-principals)
11. **`[Medium]`** What is the difference between Azure Blob Storage and Azure Files, and Service Bus vs Storage Queue? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q10--q15-azure-storage-blob-vs-files--queues-service-bus-vs-storage-queue)
12. **`[Medium]`** What are the differences between Azure Load Balancer (Layer 4) and Azure Application Gateway (Layer 7)? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q11-azure-load-balancer-architecture--types)
13. **`[Medium]`** Explain the full AWS deployment flow — from Docker image creation in CI to ECR push and EKS container rollout. → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q18-end-to-end-aws-deployment-flow-ci-build-to-ecr-and-eks-container-rollout)
14. **`[Medium]`** Which AWS services have you integrated in enterprise Java backend engineering (ECS/EKS, RDS, SQS, S3, Secrets Manager)? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q19-core-aws-services-integration-in-enterprise-java-backend-engineering)
15. **`[Medium]`** How do you securely manage database credentials and API secrets in Azure using Azure Key Vault and Secrets Store CSI Driver? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q16-azure-compliance-governance--secret-management)
16. **`[Medium]`** Explain AWS Virtual Private Cloud (VPC), Public vs Private subnets, and NAT Gateway routing for Java applications. → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q17-aws-virtual-private-cloud-vpc--network-resource-isolation)
17. **`[Hard]`** How do you achieve Zero-Downtime deployments in Azure Kubernetes Service (AKS) or Amazon EKS with Spring Boot graceful shutdown? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q13--q14-azure-compute-models-vm-vs-app-service-vs-functions-vs-aks--zero-downtime-deployments)
18. **`[Hard]`** How does container memory sizing and cgroups v2 interact with the JVM to prevent Linux OOMKilled errors (-XX:MaxRAMPercentage)? → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q20-production-kubernetes-architecture-for-java-microservices-memory-limits--cgroups-v2)
19. **`[Hard]`** Explain the compute trade-offs between Azure VMs (IaaS), App Service (PaaS), Functions (Serverless), and AKS (Containers). → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q13--q14-azure-compute-models-vm-vs-app-service-vs-functions-vs-aks--zero-downtime-deployments)
20. **`[Expert]`** Design an enterprise multi-region Kubernetes disaster recovery architecture with automated DNS failover and zero data loss. → [Solution](11_cloud_devops_docker_k8s_azure_faq.md#q13--q14-azure-compute-models-vm-vs-app-service-vs-functions-vs-aks--zero-downtime-deployments)

---

## 12 – Production Troubleshooting, Observability, Memory Diagnostics & Real-World Triage

*Companion Deep-Dive Guide: [`12_troubleshooting_performance_and_production_faq.md`](12_troubleshooting_performance_and_production_faq.md) (22 Questions & Production Solutions)*

1. **`[Easy]`** How do you perform local and remote debugging in Java applications using JDWP (Java Debug Wire Protocol)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q1--q2-local-and-remote-debugging-via-jdwp--port-distinctions)
2. **`[Easy]`** What is the difference between the application HTTP port (8080) and the remote debug port (5005)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q1--q2-local-and-remote-debugging-via-jdwp--port-distinctions)
3. **`[Easy]`** Why does increasing logging verbosity to DEBUG in production significantly degrade throughput and increase latency? → [Solution](12_troubleshooting_performance_and_production_faq.md#q13-why-increasing-log-verbosity-degrades-performance)
4. **`[Easy]`** How do you troubleshoot and fix NoSuchBeanDefinitionException in Spring Boot? → [Solution](12_troubleshooting_performance_and_production_faq.md#q16-troubleshooting-nosuchbeandefinitionexception-in-spring-boot)
5. **`[Medium]`** Service is slow, but CPU usage is low (10%). What do you check first (Database locks, thread pool exhaustion, I/O wait)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q3-service-slow-but-cpu-usage-is-low-what-to-check-first)
6. **`[Medium]`** Application runs fine for hours, then suddenly slows down. What are the common causes (Metaspace leak, GC thrashing, connection leaks)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q4-application-runs-fine-for-hours-then-suddenly-slows-down)
7. **`[Medium]`** High CPU usage (95%) with very low traffic. What investigation steps do you take (Infinite loop, JIT compilation, Stop-The-World GC)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q5-high-cpu-usage-with-very-low-traffic-investigation-steps)
8. **`[Medium]`** API response time is inconsistent: 90% of requests take 20ms, but 10% take 5000ms. How do you troubleshoot the p99 latency spike? → [Solution](12_troubleshooting_performance_and_production_faq.md#q6-inconsistent-api-response-time-without-code-changes)
9. **`[Medium]`** How do you isolate heavy asynchronous background jobs from impacting real-time user-facing API latency? → [Solution](12_troubleshooting_performance_and_production_faq.md#q7-isolating-background-jobs-from-impacting-api-latency)
10. **`[Medium]`** Why does HashMap lookup time degrade from O(1) to O(N) under high load (Hash collision attacks and mutable keys)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q10-hashmap-lookup-time-degrades-from-o1-to-on)
11. **`[Medium]`** What are Retry Storms, and how do poorly configured retries cause cascading production outages? → [Solution](12_troubleshooting_performance_and_production_faq.md#q15-retry-storms-how-poorly-configured-retries-cause-cascading-outages)
12. **`[Medium]`** How do you troubleshoot and resolve 504 Gateway Timeout and 503 Service Unavailable errors in production? → [Solution](12_troubleshooting_performance_and_production_faq.md#q17-troubleshooting-and-resolving-504-gateway-timeout-and-503-service-unavailable)
13. **`[Medium]`** You are handed a Java pull request to review. What production checklist do you follow (Concurrency, N+1 queries, security, observability)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q20-enterprise-java-pull-request-code-review-checklist)
14. **`[Hard]`** How do you debug rare production deadlocks that cannot be reproduced locally using thread dumps (jstack, top -H)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q8-debugging-rare-production-deadlocks-that-cannot-be-reproduced-locally)
15. **`[Hard]`** Process memory usage keeps increasing even though heap memory is stable and GC runs fine. How do you investigate off-heap and native memory leaks (NMT)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q9-memory-usage-keeps-increasing-with-no-obvious-heap-leaks)
16. **`[Hard]`** GC pauses increase significantly after a small code release. How do you analyze GC logs and heap allocations to locate the cause? → [Solution](12_troubleshooting_performance_and_production_faq.md#q11-gc-pauses-increase-significantly-after-a-small-release)
17. **`[Hard]`** How do you tune JVM Garbage Collectors (G1 vs ZGC) for low-latency vs high-throughput production workloads? → [Solution](12_troubleshooting_performance_and_production_faq.md#q12-jvm-tuning-throughput-vs-latency-trade-off)
18. **`[Hard]`** Why does scaling out instances from 5 to 20 sometimes worsen overall system performance instead of improving it (Connection pool saturation)? → [Solution](12_troubleshooting_performance_and_production_faq.md#q14-why-scaling-instances-worsens-overall-system-performance)
19. **`[Hard]`** A database query that takes 20ms in dev suddenly takes 10 seconds in production. What is your exact diagnostic triage runbook? → [Solution](12_troubleshooting_performance_and_production_faq.md#q18-production-database-query-suddenly-takes-10-seconds-diagnostic-triage-playbook)
20. **`[Hard]`** A production issue causes widespread failures but application logs contain zero exceptions. What diagnostic steps do you take? → [Solution](12_troubleshooting_performance_and_production_faq.md#q19-production-outage-with-no-exceptions-in-logs-silent-failure-investigation-runbook)
21. **`[Expert]`** If you inherited an unstable Java microservices platform with high latency, frequent outages, and poor observability, what would your first 90-day engineering improvement plan look like? → [Solution](12_troubleshooting_performance_and_production_faq.md#q21-the-90-day-engineering-improvement-plan-for-unstable-legacy-java-platforms)
22. **`[Expert]`** How would you modernize a 15-year-old monolithic Java enterprise application into microservices using the Strangler Fig pattern without downtime? → [Solution](12_troubleshooting_performance_and_production_faq.md#q22-modernizing-legacy-java-monoliths-via-the-strangler-fig-pattern)

---
