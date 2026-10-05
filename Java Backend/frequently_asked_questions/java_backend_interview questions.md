# Java Core & Object-Oriented Programming

1. How do you reverse a string in Java without using built-in API functions?
2. How do you convert the first half of a string to lowercase and the second half to uppercase in Java?
3. How do you find the first non-repeating character in a string?
4. How do you find the length of the longest substring without repeating characters?
5. How do you find the longest common prefix among an array of strings?
6. How do you remove duplicates from an array in a single iteration?
7. How do you rearrange an integer array so that all even numbers appear before all odd numbers?
8. How do you print prime numbers between 1 and 200 with minimal time complexity?
9. How do you reverse a singly linked list iteratively and recursively?
10. How do you verify if a path from root to leaf in a binary tree sums to a given target value?
11. What is the difference between the == operator and the equals() method in Java?
12. How does short-circuiting work with logical operators (&&, ||) in Java?
13. What is the use of constructor in Java?
14. Can you invoke a static method inside a constructor in Java?
15. Why is composition preferred over inheritance in object-oriented design?
16. When should you use an interface versus an abstract class in Java?
17. What is the difference between static and default methods in Java interfaces?
18. What is a marker interface, and what are its practical use cases?
19. Can you override a static method in Java?
20. What is method hiding in Java?
21. What is an anonymous inner class, and how is it used in Java?
22. How do you create an immutable class in Java?
23. What is the difference between shallow copy and deep copy in Java?
24. What is the difference between StringBuffer and StringBuilder?
25. How does the String Pool work in Java?
26. What is the purpose of the finally block?
27. What are the differences between final, finally, and finalize() in Java?
28. What causes a ClassCastException, and how can it be prevented?
29. Explain memory management in Java (Heap, Stack, Metaspace, GC).
30. What are some common causes of OutOfMemoryError in Java and how do you handle them?
31. What are the different types of class loaders in Java?
32. What is the Java Reflection API, and what is a practical example of its use?
33. What is a Proxy class in Java and how is it used for dynamic method invocation?
34. What is the difference between the Path and File classes in Java NIO vs IO?
35. How does Java handle tail call optimization?
36. What are text blocks, and where are they useful?
37. How does pattern matching for switch work in Java 17+?
38. What are the differences between records, standard classes, and sealed classes in modern Java?
39. Can you explain a scenario where sealed classes were beneficial in enforcing stricter type hierarchies in your project?
40. What are virtual threads in Java 21, and how do they differ from platform threads?

# Java 8 Features & Functional Programming

1. What is a functional interface in Java, and what are some commonly used built-in functional interfaces?
2. What is the significance of the @FunctionalInterface annotation in Java?
3. What is the role and purpose of the java.util.function package?
4. What is a Predicate interface, and how is it used in Java?
5. What are method references in Java?
6. What is Optional used for, and how does it help prevent NullPointerExceptions?
7. What is a Stream in Java 8, and how does it differ from a Collection?
8. How does Java Streams work internally?
9. What is the difference between map() and flatMap() in the Java Stream API?
10. How does the Stream API handle parallelism in Java 8?
11. How do you filter a list of employee names starting with "SU" using the Stream API?
12. How do you find employee names starting with 'a' or 'A' and sort them using Java Streams?
13. How do you count the number of vowels in a string using Java 8 Streams?
14. How do you find the second highest integer in an unsorted list using the Stream API?

# Collections Framework

1. What is the main difference between List and Set in Java Collections?
2. What are the key differences between ArrayList and LinkedList?
3. What is the difference between Arrays.asList() and new ArrayList<>()?
4. What are the differences between List.of() and new ArrayList<>() introduced in Java 9+?
5. How do HashSet, LinkedHashSet, and TreeSet differ in terms of ordering and performance?
6. How does a HashSet work internally, and how does it ensure unique elements?
7. What is the contract between hashCode() and equals(), and what happens if it is violated?
8. Which methods must you override when using a custom object as a Map key?
9. What are the default initial capacity, load factor, and resizing mechanisms of a HashMap?
10. Can a HashMap store null keys, and if so, at which bucket index are null keys stored?
11. What happens when two keys collide in a HashMap (same hash)?
12. What are the differences between HashMap, ConcurrentHashMap, and Hashtable?
13. How does ConcurrentHashMap work internally to achieve thread safety?
14. What is the difference between WeakHashMap and HashMap?
15. How does a TreeMap maintain sorting of its keys?
16. Can TreeMap have null keys? Why or why not?
17. How do you use EnumSet and EnumMap in Java?
18. What is the key functional and structural difference between Queue and Deque?
19. How does PriorityQueue work internally in Java?
20. What are the differences between poll(), peek(), and remove() methods in a Queue?
21. What are the differences between Iterator and ListIterator in Java?
22. What is the difference between Fail-Fast and Fail-Safe iterators?
23. What causes a ConcurrentModificationException, and how can you prevent it?
24. How do you safely remove elements from a collection while iterating over it?
25. What is the difference between Comparable and Comparator interfaces?
26. What are stable sorting algorithms and in-place sorting algorithms?
27. How do you create an immutable collection in Java?
28. How would you handle large datasets in Java using Collections?
29. How do you filter a list of employees to get those with a salary greater than 10,000?
30. How do you rearrange a list so the minimum element is at index 0 and maximum is at the last index while maintaining the original order of other elements?
31. How do you find the most frequently occurring element in a list?
32. How do you implement an LRU Cache in Java with O(1) operations?
33. How do you calculate the frequency of each element in a list?
34. How do you sort a Map by its values in Java?

# Multithreading, Concurrency & Async Programming

1. What is the difference between Runnable and Callable?
2. What happens if you invoke the run() method directly instead of start() on a Thread?
3. What does the synchronized keyword do?
4. What is the difference between sleep() and wait()?
5. What is the use of volatile vs AtomicInteger in concurrent programming?
6. What is the difference between volatile and synchronized, and when should you use each?
7. What is a deadlock and how do you prevent it in multithreaded applications?
8. Explain ExecutorService and ThreadPool in Java.
9. What is the difference between Future and CompletableFuture in asynchronous programming?
10. What is the difference between thenApply() and thenCompose() in CompletableFuture?
11. How can you leverage CompletableFuture to optimize thread management and concurrency?
12. Multiple threads update shared data (e.g. bank balance) and results are inconsistent. How do you fix it?
13. What will happen if two threads try to access a HashMap simultaneously? How will you fix it?
14. JVM doesn't exit even after main() completes. What's holding it?
15. Why might requests be waiting even when the thread pool appears to be configured properly?
16. Why might ExecutorService tasks fail silently without logging errors, and how can this be resolved?
17. Why might introducing parallel streams cause latency to increase instead of improving performance?

# Spring & Spring Boot Core

1. What are the key differences between the Spring Framework and Spring Boot?
2. What is ApplicationContext in Spring?
3. How does Spring Dependency Injection work internally?
4. How to handle dependency injection in Spring Boot?
5. Difference between @Component, @Service, @Repository, and @Controller.
6. What is a component class in Spring, and how is it detected by the container?
7. Difference between @Primary and @Qualifier in Spring.
8. When should you use @Bean vs @Autowired for database configuration or dependency management?
9. What is the Spring Bean lifecycle?
10. What is Bean scope in Spring Boot? Singleton - how to change with @Scope annotation?
11. Difference between prototype and request scope.
12. Explain the difference between @ComponentScan, @EnableAutoConfiguration, and @SpringBootApplication.
13. Explain how Spring Boot auto-configuration works internally.
14. How do you read a property value into code (e.g., a JDBC URL) using @Value or YAML?
15. How do you implement profiles in Spring Boot for multiple environments?
16. What is a ViewResolver in Spring MVC architecture, and how does it function?
17. Which Spring annotations are used for exception handling in web applications?
18. What is the role of Spring Boot Actuator in production, and how do you secure its endpoints?
19. What is your approach to developing a new module in a Spring Boot application?

# REST APIs & Web Services

1. Is REST architecture stateless or stateful, and why?
2. What is the difference between @Controller and @RestController in Spring?
3. Which annotations are used to map HTTP requests to REST endpoints in Spring?
4. Differences between @RequestParam, @PathVariable, and @RequestBody in an endpoint URL.
5. What is the semantic difference between PUT and PATCH HTTP methods?
6. How do you design idempotent operations in RESTful APIs?
7. Which common HTTP status codes (e.g., 201, 400, 401, 403, 404, 502, 503) should be returned for specific REST API responses?
8. How do you handle "404 Not Found" and "400 Bad Request" errors gracefully in Spring Boot?
9. What is a Data Transfer Object (DTO), and why is it used in API design?
10. How does request data flow through Controller, Service, and Repository layers in Spring Boot?
11. How to handle exceptions in Spring REST APIs globally using @ControllerAdvice and @ExceptionHandler?
12. What is the difference between RestTemplate, WebClient, and FeignClient?
13. Why is API versioning required, and what strategies can be used to implement it?
14. How would you design a REST API endpoint to fetch the last N orders for a customer using Spring Boot and Spring Data JPA?
15. How does Spring Boot handle high-concurrency requests coming into controller endpoints?
16. How do you implement rate limiting and throttling in Spring Boot APIs?
17. How do you configure Spring Security to require JWT authentication for a subset of endpoints while leaving others public?
18. Explain the difference between REST, gRPC, and GraphQL APIs.
19. A REST API call sometimes returns null data, sometimes throws exceptions. How will you design your service layer to handle both gracefully?

# Spring Security, Authentication & Authorization

1. What is the difference between authentication and authorization?
2. What is Spring Security, and what are the primary components of its architecture?
3. What is the execution order of filters in the Spring Security Filter Chain?
4. How does a request flow through Spring Security internally?
5. What is the role of AuthenticationManager?
6. What is UserDetailsService and when do you use it?
7. How do you implement custom authentication providers?
8. Why BCryptPasswordEncoder over plain text passwords?
9. What's @PreAuthorize vs @Secured vs @RolesAllowed?
10. How do you secure method parameters and return values?
11. How does Spring Security handle CSRF protection?
12. How does remember-me functionality work?
13. Why use JWT tokens instead of sessions?
14. Explain the structure of a JWT Token (Header, Payload, Signature) and how it is generated and validated.
15. Where should JWT tokens be stored on the client side and how can they be revoked before expiry?
16. What happens if a JWT token expires and how do you implement Refresh Tokens?
17. What security risks exist with JWT and how can they be mitigated?
18. OAuth2 vs Basic Authentication - when to use what?
19. Explain the OAuth2 Authorization Code Flow, Resource Server, Authorization Server, and Scopes.
20. Explain Client Credentials Grant and its use cases.

# Databases, JPA & Hibernate

1. Explain ACID properties and isolation levels in databases.
2. Difference between INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL JOIN.
3. SQL query to fetch the second-highest salary.
4. Write a SQL query to get department-wise average salary.
5. How do you use indexes, and what are clustered vs non-clustered indexes?
6. How do you optimize slow-running SQL queries?
7. How to call stored procedures from JDBC code?
8. How do you connect a database with your Spring Boot Application and connect multiple data sources?
9. Which JPA annotations (@Id, @GeneratedValue) are required to specify unique primary keys for entities?
10. What are entity states (Transient, Persistent, Detached, Removed) in Hibernate?
11. What is Criteria API and how does it compare to JPQL?
12. What is the difference between LAZY and EAGER fetch types, and how do they impact performance?
13. Explain lazy loading and how to avoid LazyInitializationException.
14. What is the N+1 select problem in ORM frameworks, and how do you prevent it in Spring Data JPA?
15. How does Hibernate manage caching (1st level vs 2nd level)?
16. How does dirty checking and flush mode work in Hibernate?
17. What is the difference between CascadeType.ALL and orphanRemoval?
18. How does @Transactional work in Spring, and what are transaction propagation levels and common pitfalls?
19. Optimistic vs Pessimistic locking in JPA with real examples.
20. Two users try to book the same train seat at the same time. How do you ensure only one booking goes through?
21. How do you implement pagination and sorting in Spring Data JPA?
22. How do you handle multi-tenant applications in JPA?

# Microservices Architecture & System Design

1. What are the advantages and challenges of adopting a microservices architecture?
2. What is a Bounded Context in Domain-Driven Design (DDD)?
3. What is service discovery and load balancing in Microservices Architecture?
4. What are the pros and cons of using Eureka versus Consul for service discovery?
5. What is the API Gateway pattern, and why is it used in microservices?
6. What is the difference between synchronous and asynchronous communication, and when to use each?
7. How do you ensure communication between microservices is secure?
8. What is a Circuit Breaker pattern in the context of Microservices (Resilience4j / Hystrix)?
9. How do you prevent cascading failures when a dependent microservice becomes unavailable?
10. What recovery mechanisms do you usually implement in microservices to handle failures in production?
11. How do you manage distributed transactions across microservices using Saga or Outbox patterns?
12. What is eventual consistency, and how do you handle it in distributed systems?
13. How would you implement idempotency in your service when processing payments?
14. What is the Command Query Responsibility Segregation (CQRS) pattern?
15. How do you design an event-driven architecture using messaging systems like Azure Service Bus or Kafka?
16. How do you implement distributed tracing and centralized logging in Spring Boot microservices?
17. What is semantic monitoring, and how is it used in microservices architecture?
18. What is a Consumer-Driven Contract (CDC)?
19. How do you ensure backward compatibility for APIs in microservices?
20. What is the CAP theorem?
21. How can you scale a Database?
22. How is YAML used for configuration and deployment in microservices environments?
23. Design a URL shortener service (like bit.ly) and discuss scaling it.
24. Design a system to handle millions of chat messages per second.
25. Given a huge dataset distributed across servers, find the top K elements efficiently.
26. Given a real-time stream of events, detect anomalies within a rolling window.

# Messaging Systems (Kafka & RabbitMQ)

1. What is a message broker service, and how does it fit into Java enterprise applications?
2. What is an exchange in AMQP/RabbitMQ message queues?
3. What is the difference between Kafka and RabbitMQ? When would you use each?
4. What are the components of Kafka and what is a partition in Kafka?
5. How do Kafka partitions enable scalability and what is a consumer group?
6. How do you integrate Apache Kafka with a Spring Boot application?
7. What is a Dead Letter Queue (DLQ), and when is it used?
8. How do you handle message processing failures or slow consumers in message queues?

# Cloud & DevOps (Azure, Docker & K8s)

1. What is the difference between a Docker image and a Docker container?
2. How do you create Docker containers for Spring Boot apps?
3. What is the difference between liveness and readiness probes in Kubernetes?
4. Kubernetes auto-scaling mechanisms and how it handles scaling & service discovery.
5. How do you structure a CI/CD pipeline using Jenkins or GitHub Actions to build and deploy microservices?
6. What are the differences between blue-green, canary, and rolling deployment strategies?
7. How do you monitor production microservices using tools like Actuator, ELK, Prometheus, or Azure Monitor?
8. Explain the Azure Resource Hierarchy and Resource Groups.
9. What are IAM roles, Managed Identities, and Service Principals in Azure?
10. What are the different types of storage areas in Microsoft Azure (Blob Storage vs Azure Files)?
11. How does Azure Load Balancer work, and what types are available?
12. Horizontal vs vertical scaling in Azure with examples.
13. What is Azure Virtual Machine vs Azure App Services vs Azure Functions?
14. What is Azure Kubernetes Service (AKS), and when should you use it over Azure App Service? How to achieve zero-downtime deployment?
15. What are the differences between Azure Service Bus Queue and Azure Storage Queue?
16. How do you ensure compliance, governance, and secret management in Azure?
17. What is a Virtual Private Cloud (VPC) in AWS, and how does it isolate network resources?

# Troubleshooting, Performance & Real-World Scenarios

1. How do you configure local and remote debugging for Java applications?
2. What is the difference between an application server port and a JVM remote debug port?
3. Your Java service is slow but CPU usage is low. What do you check first?
4. The application runs fine for hours, then suddenly slows down. What could be wrong?
5. High CPU usage with very low traffic — where do you investigate?
6. API response time is inconsistent without code changes. What could cause this?
7. A background job impacts API response time. How do you isolate it?
8. A deadlock happens rarely and cannot be reproduced locally. How do you debug it?
9. Memory usage keeps increasing with no obvious leaks. What do you suspect?
10. HashMap grows in size but lookup time increases. What went wrong?
11. GC pauses increase after a small release. What change could cause this?
12. JVM tuning improved latency but reduced throughput. Explain the trade-off.
13. Why might increasing log verbosity in production cause overall application performance to degrade?
14. After scaling instances, overall performance gets worse. Why?
15. How can poorly configured retry logic cause a retry storm and overload downstream services?
16. How do you troubleshoot a "NoSuchBeanDefinitionException" or missing bean error when spring component scanning fails?
17. How do you troubleshoot and resolve 504 Gateway Timeout and 503 Service Unavailable errors in production?