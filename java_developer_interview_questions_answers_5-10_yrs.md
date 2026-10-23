# Java Developer Interview Questions & Answers (5-10 Years of Experience)

## 1. Core Java & JVM

### Q1. How does HashMap work internally in Java 8 and later?

**Answer:** `HashMap` stores key-value pairs in an array of buckets. It calculates a hash from the key and uses that hash to determine the bucket index. If multiple keys map to the same bucket, a collision occurs. Entries in a bucket are usually linked, but a heavily populated bucket can be converted into a red-black tree when the implementation's conditions are met.

When inserting a key, `HashMap` checks the bucket and uses hash and equality comparisons to determine whether the key already exists. When the map grows beyond its resize threshold, it expands the table and redistributes entries.

**Important points:**
- Average lookup and insertion are generally O(1) with a suitable hash distribution.
- Poor hash distribution and collisions can reduce performance.
- Keys should not change fields used by `equals()` or `hashCode()` while stored in the map.
- `HashMap` is not thread-safe for concurrent structural modifications.

**Follow-up:** What happens if two keys have the same hash code but `equals()` returns `false`?

### Q2. What happens when two objects have the same hash code?

**Answer:** They may map to the same bucket in a hash-based collection, causing a collision. The collection then uses equality checks to distinguish the objects. Equal hash codes do not mean that two objects are equal; however, objects considered equal by `equals()` must return the same hash code.

### Q3. Why should `equals()` and `hashCode()` be overridden together?

**Answer:** Hash-based collections such as `HashMap` and `HashSet` use the hash code to locate a bucket and equality to identify the matching object. If two objects are equal according to `equals()` but have different hash codes, a collection may fail to find an existing key or prevent a duplicate as expected. The contract requires equal objects to have equal hash codes.

### Q4. How does ConcurrentHashMap differ from HashMap and Hashtable?

**Answer:** `HashMap` is not thread-safe. `Hashtable` synchronizes its methods, which can create contention. `ConcurrentHashMap` is designed for concurrent access and uses finer-grained coordination and atomic operations so multiple threads can often read and update different parts of the map concurrently. It does not permit `null` keys or values. Its iterators are weakly consistent rather than fail-fast in the same way as typical `HashMap` iterators.

### Q5. What is fail-fast iteration, and what are its limitations?

**Answer:** Many standard collection iterators detect structural modifications made outside the iterator and may throw `ConcurrentModificationException`. This is a best-effort bug-detection mechanism, not a thread-safety guarantee. It should not be relied upon to coordinate concurrent access. Use the iterator's supported removal method, appropriate synchronization, or a concurrent collection when needed.

### Q6. How do ArrayList and LinkedList differ in practical performance?

**Answer:** `ArrayList` uses a resizable array, giving fast indexed reads and good cache locality. Appending is amortized O(1), although resizing occasionally costs O(n). `LinkedList` stores nodes linked together; indexed access is O(n), and insertion or removal is O(1) only when the relevant node is already known. In many ordinary application workloads, `ArrayList` is the better default because of memory and locality advantages.

### Q7. How would you design an immutable class?

**Answer:** Make the class final when appropriate, keep fields private and final, initialize them in the constructor, expose no mutators, and defensively copy mutable inputs and outputs. Ensure that methods do not expose internal mutable state. Immutability simplifies reasoning about shared data and can improve thread safety.

### Q8. What is the difference between shallow copy and deep copy?

**Answer:** A shallow copy duplicates the outer object but retains references to the same nested objects. A deep copy also duplicates the relevant nested mutable objects. Deep copying can provide stronger independence, but it costs more and requires a clear definition of which parts of the object graph should be copied.

### Q9. What is the Java Memory Model?

**Answer:** The Java Memory Model defines how threads interact through memory and which visibility and ordering guarantees the language provides. It specifies happens-before relationships, including those established by monitor locks, volatile variables, thread start/join, and concurrency utilities. Correct concurrent programs should use these defined synchronization mechanisms rather than rely on incidental behavior.

### Q10. How do volatile, synchronized, and AtomicInteger differ?

**Answer:** `volatile` provides visibility and ordering guarantees for reads and writes of a variable, but does not make compound operations such as `count++` atomic. `synchronized` provides mutual exclusion and memory-visibility guarantees around a critical section. `AtomicInteger` supports atomic operations such as increment and compare-and-set without requiring a synchronized block. The right choice depends on whether the requirement is visibility, mutual exclusion, or atomic state updates.

### Q11. How do locks differ from synchronized blocks?

**Answer:** Both can provide mutual exclusion. The explicit `Lock` API offers capabilities such as interruptible acquisition, timed acquisition, and optional fairness policies. It also requires careful unlocking, typically in a `finally` block. `synchronized` has simpler automatic lock release and is often sufficient for straightforward critical sections.

### Q12. What causes deadlocks, and how can they be prevented?

**Answer:** A deadlock occurs when threads wait indefinitely for resources held by one another. Common contributing conditions include mutual exclusion, holding a resource while waiting for another, lack of forced preemption, and circular waiting. Reduce risk by acquiring locks in a consistent global order, minimizing lock scope, avoiding unnecessary nested locks, and using timeouts where appropriate. Thread dumps can help diagnose deadlocks.

### Q13. How does ThreadLocal work, and why can it cause memory leaks?

**Answer:** `ThreadLocal` provides each thread with its own value. It is useful for thread-confined context, but values can remain associated with long-lived worker threads in a pool. If values are not removed, they may retain large objects or request-specific data longer than intended. Call `remove()` in a `finally` block when the value's lifetime ends, and consider whether explicit parameter passing or another context mechanism is clearer.

### Q14. How do ExecutorService and thread pools work?

**Answer:** An executor separates task submission from the mechanics of running tasks. A thread pool reuses worker threads, reducing thread-creation overhead. Pool size, queue capacity, rejection policy, task duration, and shutdown behavior all affect throughput and latency. Unbounded queues can hide overload and cause memory pressure; pools should be sized based on the workload and measured behavior.

### Q15. What is CompletableFuture, and how should asynchronous failures be handled?

**Answer:** `CompletableFuture` represents a result that may become available later and supports composing dependent stages. Methods such as `thenApply`, `thenCompose`, and `allOf` help build asynchronous pipelines. Use `thenCompose` to flatten dependent asynchronous work, and handle failures with methods such as `exceptionally`, `handle`, or `whenComplete`. Choose an appropriate executor for blocking work instead of accidentally occupying a shared common pool.

### Q16. How do virtual threads differ from platform threads?

**Answer:** Platform threads are backed more directly by operating-system threads. Virtual threads are lightweight threads managed by the Java runtime and can make thread-per-task designs practical for applications with many blocking I/O operations. They do not make CPU-bound work intrinsically faster and do not remove database connection, remote-service, or other resource limits. They are available in modern Java releases; verify the target JDK before using them.

### Q17. How does garbage collection work in Java?

**Answer:** Garbage collection reclaims heap memory occupied by objects that are no longer reachable. Modern collectors use different strategies to balance throughput, pause time, and memory overhead. A GC event is not necessarily a problem. Investigate allocation rates, heap occupancy, pause times, and application latency before changing collector settings.

### Q18. What are heap, stack, metaspace, and direct memory?

**Answer:** The heap stores objects and arrays and is managed by the garbage collector. Each thread has a stack containing frames for method calls, local variables, and related execution state. Metaspace stores class metadata in native memory. Direct buffers can use native memory outside the ordinary Java heap. Memory troubleshooting should consider both heap and native-memory usage.

### Q19. How would you investigate an OutOfMemoryError?

**Answer:** Identify the specific error message because heap exhaustion, metaspace exhaustion, native-thread exhaustion, and direct-buffer exhaustion have different causes. Review JVM logs and memory metrics, capture a heap dump when safe, and analyze retained objects and dominator trees. Check for unbounded caches, retained collections, thread-local values, excessive concurrency, and unexpectedly large workloads. Fix the retention or capacity problem rather than simply increasing memory without evidence.

### Q20. How would you investigate high CPU usage in a Java application?

**Answer:** Confirm the affected process and time window, then capture several thread dumps or a profiler recording. Correlate high-CPU threads with stack traces, and check for tight loops, excessive serialization, expensive regular expressions, lock contention, or excessive garbage collection. Reproduce the workload where possible, measure the proposed fix, and compare latency and throughput before and after.

### Q21. How do strong, weak, soft, and phantom references differ?

**Answer:** A strong reference normally keeps an object reachable. A weakly referenced object can be reclaimed when no strong references keep it alive. Soft references may be cleared under memory pressure, but they are not a reliable cache policy. Phantom references support post-mortem cleanup coordination through a reference queue and do not allow the referent to be retrieved normally.

### Q22. How do Java Streams work, and when should parallel streams be avoided?

**Answer:** Streams express data-processing pipelines using intermediate operations, which are generally lazy, and terminal operations, which trigger processing. Parallel streams split work across multiple tasks, but splitting, coordination, and merging introduce overhead. Avoid them for small workloads, blocking I/O, shared mutable state, or operations whose order is important unless the design explicitly accounts for those concerns. Benchmark representative data before choosing parallel execution.

### Q23. What is type erasure in Java Generics?

**Answer:** Java generics primarily provide compile-time type checking. During compilation, generic type information is largely erased or translated to bounds, with bridge methods generated where needed. This explains restrictions such as being unable to use a parameterized type directly in many `instanceof` checks or create `new T()` without additional mechanisms.

### Q24. How do records and sealed classes help?

**Answer:** Records provide a concise way to model data-oriented values, generating members such as accessors, `equals()`, `hashCode()`, and `toString()`. Sealed classes restrict which classes may extend or implement a type, making a hierarchy more explicit. Pattern matching features can reduce boilerplate when inspecting types. Confirm the exact feature availability for the project's Java version.

### Q25. How would you optimize a Java application safely?

**Answer:** Start by defining the performance problem and measuring a representative baseline. Use metrics, profiling, thread dumps, GC logs, and database traces to identify the bottleneck. Make one targeted change at a time, validate correctness, and compare latency, throughput, CPU, memory, and error rates. Avoid speculative micro-optimizations that do not address a measured bottleneck.

## 2. Database, SQL & PostgreSQL

### Q26. How does a database index work internally?

**Answer:** An index is an auxiliary data structure that helps the database locate rows without scanning the entire table. A B-tree index, for example, organizes keys in a structure that supports efficient searches and ordered range scans. Indexes consume storage and add overhead to inserts, updates, and deletes, so they should support real query patterns.

### Q27. Why might PostgreSQL choose a sequential scan instead of an index scan?

**Answer:** The planner estimates the cost of available plans using statistics, selectivity, table size, and I/O assumptions. If a query returns a large proportion of a table, a sequential scan may be cheaper than many index lookups followed by table-page reads. Stale statistics, low selectivity, type mismatches, or query expressions can also affect index use. Check `EXPLAIN (ANALYZE, BUFFERS)` and current statistics before forcing a plan.

### Q28. How do B-tree, Hash, GIN, and GiST indexes differ in PostgreSQL?

**Answer:** B-tree is the general-purpose choice for equality, range comparisons, and ordering. Hash indexes support equality comparisons. GIN is useful for values containing multiple searchable components, such as arrays, some JSONB queries, and full-text search. GiST is an extensible framework used for specialized searches such as geometric or range operations. Choose an index type based on the operators and workload.

### Q29. What is a covering index, and how does an index-only scan work?

**Answer:** A covering index contains the columns needed by a query, allowing the database to potentially answer it from the index. PostgreSQL can perform an index-only scan when the index contains the required values, but it may still need heap visibility checks unless the visibility map shows that relevant pages are all-visible. `INCLUDE` columns can add payload fields to a B-tree index without making them search-key columns.

### Q30. How do you analyze a slow SQL query?

**Answer:** Capture the actual query and parameters, then use `EXPLAIN (ANALYZE, BUFFERS)` in a safe environment or with care in production because `ANALYZE` executes the statement. Compare estimated and actual row counts, scan types, join strategies, sorts, buffer reads, and time spent in each node. Check statistics, indexes, data distribution, locking, and application-level connection delays. Change the query or schema based on evidence.

### Q31. What are normalization and denormalization?

**Answer:** Normalization organizes data to reduce duplication and update anomalies. Denormalization deliberately duplicates or precomputes information to improve specific read patterns or reduce expensive joins. Denormalization introduces consistency and maintenance costs, so it should follow measured requirements rather than be applied automatically.

### Q32. Explain ACID properties.

**Answer:** Atomicity means a transaction succeeds as a unit or is rolled back. Consistency means transactions preserve defined integrity rules. Isolation controls how concurrent transactions interact. Durability means committed changes survive failures according to the database's durability guarantees and configuration. ACID helps preserve correctness when multiple operations must be treated as one transaction.

### Q33. What are transaction isolation levels?

**Answer:** Isolation levels determine which effects of concurrent transactions can be observed. Common anomalies include dirty reads, non-repeatable reads, and phantoms. PostgreSQL implements `READ UNCOMMITTED` with behavior equivalent to `READ COMMITTED`; its default is `READ COMMITTED`. `REPEATABLE READ` provides a stable transaction snapshot, while `SERIALIZABLE` adds checks that can abort transactions whose concurrent execution cannot be safely serialized. Applications must be prepared to retry eligible aborted transactions.

### Q34. What is the difference between optimistic and pessimistic locking?

**Answer:** Optimistic locking allows concurrent work and detects conflicting updates, commonly using a version column. Pessimistic locking acquires a database lock to prevent conflicting operations while a transaction holds it. Optimistic locking can work well when conflicts are uncommon; pessimistic locking can be useful when contention is expected and waiting is acceptable. Both require careful transaction boundaries and retry or failure handling.

### Q35. How do database deadlocks occur?

**Answer:** A deadlock occurs when transactions hold locks that other transactions need, forming a cycle. Databases such as PostgreSQL detect deadlocks and abort one transaction to break the cycle. Reduce risk by accessing rows in a consistent order, keeping transactions short, indexing predicates appropriately, and avoiding user interaction while holding locks. Applications should handle and, when appropriate, retry deadlock victims.

### Q36. How do MVCC and VACUUM work in PostgreSQL?

**Answer:** PostgreSQL's multiversion concurrency control (MVCC) maintains row versions so transactions can observe an appropriate snapshot without readers routinely blocking writers. Updates and deletes can leave dead tuples. VACUUM makes space reusable and supports transaction-ID wraparound prevention; routine autovacuum is important for healthy database operation. Long-running transactions can delay cleanup, so monitor them along with table and vacuum statistics.

### Q37. How would you update millions of database records safely?

**Answer:** Define correctness, locking, and recovery requirements first. Consider batching by primary-key ranges, committing manageable batches, and making the job restartable and idempotent. Monitor replication lag, WAL generation, lock waits, disk usage, and application latency. For major transformations, test on production-like data and define a rollback or forward-recovery strategy.

### Q38. How would you paginate millions of rows?

**Answer:** Large `OFFSET` values can become expensive because the database may need to walk past many rows. Keyset pagination uses the last seen sort key, such as `WHERE id > :lastId ORDER BY id LIMIT :pageSize`, supported by a suitable index. Use a stable ordering and account for concurrent changes. Offset pagination can still be appropriate when users need direct access to numbered pages and the dataset is modest.

### Q39. How do connection pools work, and how should pool size be chosen?

**Answer:** A connection pool reuses database connections rather than opening a new connection for every operation. Too few connections can queue requests; too many can overload the database through excessive concurrent work. Consider database capacity, query duration, number of application instances, workload mix, and measured wait times. Pool size is a system-wide capacity decision, not just an application setting.

### Q40. How do you troubleshoot connection pool exhaustion?

**Answer:** Inspect active, idle, and pending connection counts, connection-acquisition latency, query duration, and transaction duration. Look for slow queries, leaked connections, long transactions, blocked work, and traffic spikes. Ensure connections are closed through managed resource handling and transaction boundaries are appropriate. Increasing the pool size without checking database capacity can make the incident worse.

### Q41. What is the N+1 query problem?

**Answer:** The N+1 problem occurs when an initial query retrieves a list and the application issues an additional query for each item, often through ORM lazy loading. It can create many database round trips. Solutions include fetch joins, entity graphs, batch fetching, projections, or explicit queries. Choose the strategy based on the result size and avoid fetching large object graphs unnecessarily.

### Q42. How would you design an order-processing schema?

**Answer:** A basic design might include `customers`, `orders`, `order_items`, `products`, and `payments`. Use primary keys, foreign keys, appropriate unique constraints, and indexes aligned with common access paths. Store the order's agreed price and product details where historical accuracy requires a snapshot rather than relying solely on the current product record. Define transaction boundaries and payment-state transitions explicitly.

### Q43. When would you use partitioning, read replicas, or sharding?

**Answer:** Partitioning divides one logical table into manageable parts, often to improve maintenance and queries that can prune partitions. Read replicas can scale read traffic but may lag behind the primary. Sharding distributes data across multiple database nodes and adds routing, rebalancing, and cross-shard query complexity. Choose the simplest option that meets measured scale and availability requirements.

### Q44. How do you maintain consistency when concurrent transactions update the same row?

**Answer:** Use constraints to protect invariants and select a concurrency strategy suited to the operation. Options include atomic conditional updates, optimistic version checks, row locks, or serializable transactions. For inventory, for example, an atomic update with a condition such as `WHERE quantity >= :requested` can prevent the quantity from becoming negative. Check the affected-row count to determine whether the update succeeded.

### Q45. How would you investigate a sudden increase in database latency?

**Answer:** Correlate the start time with traffic, deployments, query changes, lock waits, CPU, I/O, connection-pool waits, and replication activity. Identify the slowest statements and compare their plans and row counts with normal behavior. Check for blocking transactions, missing or unused indexes, stale statistics, resource saturation, and changes in data volume. Mitigate safely before making structural changes.

## 3. Spring Boot & Spring Framework

### Q46. How does dependency injection work in Spring?

**Answer:** Spring creates and manages application objects called beans and supplies their dependencies through its container. Constructor injection makes required dependencies explicit and simplifies unit testing. Spring resolves dependencies using type, qualifiers, primary candidates, and bean definitions. Circular dependencies often indicate a design problem and should usually be refactored rather than worked around.

### Q47. What is the Spring bean lifecycle?

**Answer:** Spring instantiates a bean, injects its dependencies, and invokes relevant awareness and initialization callbacks. Bean post-processors can inspect or wrap beans, including creating proxies. During context shutdown, destruction callbacks can release resources. Exact steps depend on bean scope and configuration; prototype beans do not receive the same automatic destruction management as singleton beans.

### Q48. How do `@Component`, `@Service`, `@Repository`, and `@Bean` differ?

**Answer:** `@Component` marks a class as a candidate for component scanning. `@Service` and `@Repository` are specialized stereotypes that communicate service and persistence roles; `@Repository` can also participate in persistence exception translation. `@Bean` is placed on a configuration method whose return value is registered in the Spring container, useful especially for configuring third-party classes.

### Q49. How does Spring Boot auto-configuration work?

**Answer:** Spring Boot configures beans based on the classpath, existing beans, properties, and conditional rules. Auto-configuration is designed to provide sensible defaults while allowing explicit customization. When unexpected behavior occurs, inspect the condition evaluation report, active profiles, configuration properties, and bean definitions.

### Q50. Why can self-invocation bypass Spring annotations?

**Answer:** Many Spring features, including common proxy-based transaction and caching support, intercept calls that pass through a Spring proxy. When a method calls another method on `this`, the call may bypass the proxy, so the annotation on the called method may not take effect. Solutions include moving the operation to another bean, redesigning the boundary, or using a different supported interception approach.

### Q51. How does `@Transactional` work?

**Answer:** In typical proxy-based Spring configuration, a proxy intercepts a method call, starts or joins a transaction according to the configured manager and propagation, and commits or rolls back based on the outcome and rollback rules. The transaction normally applies to the configured transactional resource. It does not automatically create one atomic transaction across arbitrary databases and remote HTTP calls.

### Q52. What are transaction propagation levels?

**Answer:** `REQUIRED` joins an existing transaction or creates one. `REQUIRES_NEW` suspends an existing transaction and starts a separate one. `SUPPORTS` participates if a transaction exists but does not require one. Other options include `MANDATORY`, `NOT_SUPPORTED`, `NEVER`, and `NESTED`, subject to transaction-manager support. Choose propagation based on the intended consistency boundary, not just convenience.

### Q53. Why might `@Transactional` fail to roll back?

**Answer:** Common reasons include a call bypassing the proxy, an exception type that does not match rollback rules, an exception being caught and suppressed, the method running outside the expected transaction, or work using a different resource or transaction manager. Check logs, propagation, proxy boundaries, rollback rules, and the actual exception path.

### Q54. How do checked and unchecked exceptions affect rollback?

**Answer:** By default, Spring's declarative transaction behavior typically rolls back for unchecked exceptions (`RuntimeException`) and `Error`, but not checked exceptions. Rollback rules can be configured, for example with `rollbackFor`. Choose rules that reflect business consistency requirements and ensure exceptions are not unintentionally swallowed.

### Q55. How does Spring MVC process a request?

**Answer:** In a typical Spring MVC application, a servlet container receives the request and routes it through the `DispatcherServlet`. Handler mappings identify a controller, argument resolvers and converters prepare inputs, and the controller executes. Return values are processed by return-value handlers and message converters or view resolvers. Exception resolvers and configured filters or interceptors may also participate.

### Q56. How do filters, interceptors, and AOP differ?

**Answer:** Servlet filters operate at the servlet request/response layer and can wrap requests before they reach Spring MVC. Spring MVC interceptors work around handler execution. AOP applies cross-cutting behavior around Spring-managed method execution, subject to the selected proxy or weaving mechanism. Use the layer that matches the requirement, such as request-wide security, controller handling, or service-level metrics.

### Q57. How would you implement centralized exception handling in a REST API?

**Answer:** Use `@RestControllerAdvice` with targeted `@ExceptionHandler` methods to map known exceptions to appropriate HTTP status codes and a consistent error body. Include a stable error code, safe user-facing message, and correlation ID where useful. Avoid returning stack traces or internal details to clients; log diagnostic context securely on the server.

### Q58. How do you validate incoming REST requests?

**Answer:** Use Bean Validation annotations on request DTOs and trigger validation at the controller boundary. Add domain-level checks for business rules that cannot be expressed as simple field constraints. Return a consistent response for validation failures and avoid binding external input directly to persistence entities when that creates mass-assignment or coupling risks.

### Q59. How do authentication and authorization differ in Spring Security?

**Answer:** Authentication establishes who or what the caller is. Authorization determines what that identity is allowed to do. Spring Security uses a filter chain and security context to apply authentication mechanisms and authorization rules. Secure endpoints with least privilege, validate identities and credentials appropriately, and test both permitted and denied cases.

### Q60. How would you secure a REST API with JWT?

**Answer:** Validate the token's signature, allowed algorithm, issuer, audience where applicable, and expiry before trusting its claims. Map validated claims to authorities and enforce authorization at appropriate boundaries. Use TLS, short-lived access tokens where appropriate, a safe key-rotation strategy, and secure storage practices. JWTs are signed, not necessarily encrypted, so do not put secrets in their payload.

### Q61. How do Spring Data JPA, Hibernate, and the persistence context relate?

**Answer:** Spring Data JPA provides repository abstractions. JPA defines the persistence API and semantics, while Hibernate is a common implementation. The persistence context tracks managed entities and can synchronize changes to the database through dirty checking and flushing. Understanding entity states, flush timing, and transaction boundaries helps avoid unexpected queries and memory growth.

### Q62. How do lazy loading, eager loading, and N+1 relate?

**Answer:** Lazy loading defers retrieving related data until it is accessed; eager loading requests it earlier according to mapping and provider behavior. Neither automatically guarantees an efficient query plan. Accessing a lazy relationship repeatedly can cause N+1 queries, while overly broad eager loading can retrieve unnecessary data. Fetch joins, entity graphs, projections, and batch fetching can help.

### Q63. How should configuration be managed across environments?

**Answer:** Externalize environment-specific values into configuration properties and environment variables or a configuration service. Keep secrets in a secrets manager or secure deployment mechanism, not in source control. Validate required configuration at startup, document defaults, and ensure changes are auditable. If configuration must change dynamically, define refresh, validation, consistency, and rollback behavior explicitly.

### Q64. How would you add health checks, metrics, and graceful shutdown?

**Answer:** Spring Boot Actuator can expose health and metrics endpoints. Distinguish liveness—whether the process should continue running—from readiness—whether it should receive traffic. Instrument key business and technical metrics, protect management endpoints, and configure shutdown to stop accepting new work while allowing in-flight requests to finish within a bounded period.

### Q65. How would you troubleshoot high latency in Spring Boot?

**Answer:** Break the request path into controller, service, database, remote calls, and serialization. Use traces, metrics, structured logs, thread dumps, and database diagnostics to find where time is spent. Check pool exhaustion, blocking calls, lock contention, excessive queries, GC pauses, and downstream timeouts. Fix the measured bottleneck and validate with representative load.

## 4. Microservices & Distributed Systems

### Q66. When should you choose microservices over a modular monolith?

**Answer:** Microservices can help when independently deployable domains need separate scaling, ownership, or release cycles. They also add network failures, operational overhead, distributed observability, and consistency challenges. A well-structured modular monolith is often a better starting point when independent deployment or scaling is not yet justified.

### Q67. How do you identify service boundaries?

**Answer:** Start with business capabilities, domain concepts, ownership, data invariants, and change patterns. Bounded contexts help define where models and terms have consistent meanings. Avoid splitting services solely by technical layer or creating one service per database table. Validate boundaries against real workflows and cross-service dependencies.

### Q68. How do synchronous and asynchronous communication differ?

**Answer:** Synchronous HTTP or RPC allows a caller to receive a direct response but couples its availability and latency to the downstream service. Asynchronous messaging decouples producer and consumer timing and can absorb bursts, but introduces eventual consistency, duplicate delivery, ordering, and observability challenges. Choose based on the workflow's latency and consistency requirements.

### Q69. What are API Gateway and service discovery?

**Answer:** An API Gateway provides an entry point for concerns such as routing, authentication integration, rate limiting, and request policies. Service discovery helps clients or infrastructure locate service instances dynamically. Neither replaces good service ownership, timeout configuration, authorization, or health management.

### Q70. How does load balancing work?

**Answer:** A load balancer distributes requests across service instances according to an algorithm and eligibility rules. Round robin rotates among instances, weighted round robin gives some instances more traffic, and least-connections or least-request approaches consider active work. Health checks and timeouts prevent routing to known-unhealthy instances, but health checks must reflect useful readiness signals.

### Q71. How should retries, backoff, jitter, and timeouts work together?

**Answer:** Set a deadline or timeout for the overall operation and for individual attempts. Retry only failures that are likely to be transient and operations that are safe to repeat. Exponential backoff spaces retries out, while jitter reduces synchronized retry bursts. Bound the number of attempts and avoid retries at multiple layers that multiply traffic during an outage.

### Q72. What is a circuit breaker?

**Answer:** A circuit breaker tracks failures or slow calls and can stop sending requests to a failing dependency temporarily. In the closed state calls flow normally; in the open state calls are rejected or fail fast; in half-open state a limited number of trial calls determine whether recovery has occurred. Circuit breakers complement, rather than replace, timeouts, bulkheads, and sensible fallbacks.

### Q73. How do you implement idempotency for payment or order APIs?

**Answer:** Accept a client-provided idempotency key and persist it with the operation's status and result under a uniqueness constraint. Repeated requests with the same key should return the original result or a clearly defined in-progress response rather than perform the business action twice. Define key scope, retention, request-payload matching, and behavior after failures. Coordinate with downstream providers when they support their own idempotency keys.

### Q74. How do you handle duplicate Kafka messages?

**Answer:** Assume that consumers may receive a message more than once under common delivery and retry scenarios. Make handlers idempotent using a message ID, unique business key, deduplication record, or atomic state transition. Commit offsets only at a point consistent with the processing guarantee. Coordinate database writes and offset management carefully because they are not automatically one atomic transaction.

### Q75. Explain Kafka partitions, consumer groups, offsets, and ordering.

**Answer:** A topic is divided into partitions, which provide ordered logs. Consumers in a consumer group divide partition assignments, and a given partition is processed by one consumer in that group at a time. Offsets track positions in a partition. Ordering is guaranteed within a partition, not across the entire topic, so related messages may need a consistent key to land in the same partition.

### Q76. What is the transactional outbox pattern?

**Answer:** The outbox pattern writes the business change and an event record to the same database transaction. A separate publisher relays outbox records to a broker, avoiding the failure window where the database commits but event publication does not happen. The relay may publish duplicates, so consumers should be idempotent. Cleanup, ordering, retries, and outbox growth also need attention.

### Q77. What is the Saga pattern?

**Answer:** A Saga coordinates a business workflow across multiple local transactions. Each step commits locally; if a later step fails, compensating actions may reverse the business effect where possible. Orchestration uses a central coordinator, while choreography uses events and local reactions. Compensation is not the same as a database rollback and may not perfectly undo external side effects.

### Q78. How do you maintain consistency across services?

**Answer:** Define the business invariant and acceptable consistency delay first. Common techniques include local transactions, transactional outbox, idempotent consumers, Saga workflows, reconciliation jobs, and carefully designed APIs. Avoid assuming that a distributed workflow is atomic. Make intermediate states visible and design recovery for partial failure.

### Q79. What are at-most-once, at-least-once, and exactly-once guarantees?

**Answer:** At-most-once processing avoids duplicate processing but may lose work. At-least-once processing retries until success is acknowledged, so duplicates are possible. Exactly-once guarantees are always scoped to a particular system or transaction boundary; end-to-end business effects across databases and external services still require careful coordination and idempotency.

### Q80. How do you version APIs safely?

**Answer:** Prefer backward-compatible changes such as adding optional fields with safe defaults. Avoid changing the meaning or type of existing fields unexpectedly. For breaking changes, use an explicit versioning and migration strategy, measure client usage, publish a deprecation plan, and maintain contract tests. Versioning can be done in paths, headers, or other documented mechanisms.

### Q81. How do you secure service-to-service communication?

**Answer:** Use TLS and, where appropriate, mutual TLS or workload identity. Authenticate services and authorize specific actions rather than trusting network location alone. Store credentials securely, rotate them, apply least privilege, and audit sensitive operations. Also validate incoming data and set network and request limits.

### Q82. How do you trace requests across services?

**Answer:** Propagate a trace context or correlation ID through HTTP calls and message headers. Emit structured logs and distributed traces with consistent service and operation attributes. Avoid placing secrets or personal data in trace attributes. Use traces to identify latency and failure paths, and use metrics for aggregated alerting.

### Q83. How do you prevent cascading failures and retry storms?

**Answer:** Use bounded timeouts, limited retries with backoff and jitter, circuit breakers, bulkheads, concurrency limits, and load shedding where appropriate. Apply retries at a clearly owned layer and budget them against an overall deadline. Monitor saturation and fail quickly when continuing would worsen the outage.

### Q84. What should happen when a downstream service becomes slow or unavailable?

**Answer:** Set a deadline, stop waiting when it expires, and avoid unbounded queues. Use a fallback only if it preserves the product's correctness and clearly communicates stale or incomplete data. Consider serving cached data, deferring noncritical work, or returning a controlled error. Protect the remaining system with bulkheads and monitor recovery.

### Q85. How would you decompose a monolith safely?

**Answer:** Map business capabilities and dependencies, then identify a boundary with clear ownership and manageable data coupling. Add characterization tests and observability before changing behavior. Extract incrementally using a strangler approach, define API or event contracts, and plan data ownership and rollback. Avoid a big-bang rewrite unless there is a compelling reason and a credible migration plan.

## 5. System Design

### Q86. What is your approach to a system design interview?

**Answer:** Clarify functional requirements, non-functional requirements, scale, and constraints. Estimate traffic and storage, define APIs and data models, sketch the main components and data flow, then explore bottlenecks, consistency, security, and failure handling. Explain trade-offs and revise the design as new constraints emerge rather than jumping straight to technologies.

### Q87. How do you estimate traffic and storage?

**Answer:** Estimate average and peak requests per second, payload sizes, read/write ratios, retention periods, and growth. For storage, include indexes, replication, metadata, and overhead rather than only raw business data. State assumptions clearly and use rough estimates to identify likely bottlenecks; refine them when requirements become more precise.

### Q88. How do horizontal and vertical scaling differ?

**Answer:** Vertical scaling adds resources to a single machine and is often simpler but has hardware and cost limits. Horizontal scaling adds instances and can improve capacity and resilience, but requires load balancing, coordination, statelessness or shared state, and handling partial failures. Stateful components may need different scaling strategies.

### Q89. How do load balancers, reverse proxies, and API gateways differ?

**Answer:** A reverse proxy accepts requests and forwards them to upstream servers. A load balancer distributes requests across targets and may operate at different network layers. An API Gateway adds API-oriented policies such as authentication integration, rate limiting, routing, and request transformation. These roles can overlap in a single product.

### Q90. How do you design a highly available system?

**Answer:** Identify failure domains and remove single points of failure across compute, networking, storage, and deployment. Use health-based routing, redundancy, tested backups, and recovery procedures. Define availability objectives, recovery time objective (RTO), and recovery point objective (RPO). High availability requires testing failover and recovery, not just deploying multiple instances.

### Q91. How do you choose between Redis, PostgreSQL, and NoSQL?

**Answer:** PostgreSQL is a strong fit for relational data, transactions, constraints, and flexible SQL queries. Redis is useful for low-latency in-memory access patterns such as caching, counters, and selected coordination use cases. NoSQL databases can fit access patterns requiring specific scale, data models, or distribution characteristics. Choose based on query patterns, consistency, durability, operational expertise, and cost.

### Q92. How do cache-aside, write-through, and write-behind differ?

**Answer:** In cache-aside, the application checks the cache and loads from the database on a miss. Write-through updates the cache as part of the write path. Write-behind updates the cache first and persists later, which can improve write latency but introduces durability and recovery risks. Every approach needs a clear invalidation and consistency strategy.

### Q93. How do you prevent cache stampedes and stale data?

**Answer:** A cache stampede occurs when many requests simultaneously recompute or fetch an expired value. Techniques include request coalescing, short-lived locks, randomized TTLs, background refresh, and serving stale data where acceptable. Use explicit invalidation or versioning when correctness requires it, and define behavior when the cache itself is unavailable.

### Q94. How do rate limiters work?

**Answer:** A rate limiter constrains request volume over time. Fixed windows are simple but can allow bursts around window boundaries. Sliding windows improve boundary behavior, token buckets allow controlled bursts at a defined refill rate, and leaky buckets smooth output. In a distributed deployment, shared state, atomic updates, clock behavior, and the cost of coordination matter.

### Q95. How would you design a URL shortener?

**Answer:** Provide an API to create a short code and another to redirect from the code to the original URL. Store the mapping in a durable database and add a cache for hot lookups. Generate collision-resistant codes and enforce uniqueness, define expiration and abuse controls, and decide how click counts are recorded without slowing redirects. Consider read-heavy traffic, cache invalidation, analytics, and availability.

### Q96. How would you design a notification system?

**Answer:** Accept a notification request, validate it, persist its status, and enqueue work for channel-specific workers. Use retry policies, provider rate limits, idempotency, dead-letter handling, and delivery-status tracking. Prioritize messages where required and isolate slow providers. Protect personal information and provide monitoring for queue age, failures, and delivery latency.

### Q97. How would you design an order and payment system?

**Answer:** Model order and payment states explicitly and enforce valid transitions. Persist the order and relevant events transactionally, then coordinate external payment calls through idempotent APIs and asynchronous workflows where appropriate. Handle timeouts as ambiguous outcomes rather than assuming failure, reconcile with the payment provider, and use an outbox or Saga pattern when multiple services are involved.

### Q98. How would you design a high-traffic like service?

**Answer:** Define whether the system must immediately reflect every like or can tolerate eventual consistency. Enforce one active like per user and item using a unique database constraint or equivalent atomic operation. Cache or aggregate counts for fast reads, buffer high-volume events where suitable, and reconcile derived counts. Make unlike operations idempotent and avoid relying on a cache as the only durable record unless that loss risk is explicitly accepted.

### Q99. How would you design a distributed job scheduler?

**Answer:** Store job definitions, schedules, execution state, and next-run times durably. Workers claim due jobs using leases or atomic state transitions, execute with bounded concurrency, and record results. Design for duplicate delivery, worker crashes, retries, misfires, and time zones. Use idempotent job handlers and define whether overlapping executions are permitted.

### Q100. How would you design a real-time chat system?

**Answer:** Use persistent connections such as WebSockets for real-time delivery, with authenticated sessions and a message service backed by durable storage. Define conversation membership, message ordering, delivery acknowledgments, reconnect behavior, and offline delivery. Scale connection management separately from persistence and fan-out, and use message queues or event streams when decoupling is valuable.

### Q101. How do you choose SQL versus NoSQL?

**Answer:** Begin with access patterns, data relationships, transaction needs, consistency requirements, and operational constraints. Relational databases provide strong querying and integrity features; document, key-value, wide-column, and graph databases optimize different models and access patterns. Avoid choosing NoSQL simply because a system is expected to be large—many high-scale systems work well with relational databases.

### Q102. How does leader-follower replication work?

**Answer:** A leader accepts writes and replicates changes to followers, which may serve reads depending on the consistency model. Replication can be synchronous or asynchronous, with different durability and latency trade-offs. Asynchronous replicas may return stale data. Failover requires careful leader election or promotion, fencing against split brain, and a strategy for replication lag and lost acknowledged writes.

### Q103. Explain consistency, availability, and partition tolerance.

**Answer:** The CAP theorem concerns behavior during a network partition: a distributed system cannot guarantee both strong consistency and availability for every request during that partition. Systems make trade-offs in how they handle requests when nodes cannot communicate. CAP is not a simple rule that every database must permanently choose only two properties; normal operating conditions and other consistency models matter too.

### Q104. How do you design graceful degradation?

**Answer:** Identify which features are essential and which can be disabled, delayed, or served from cached data during an incident. Isolate dependencies, set deadlines, and define safe fallback behavior. Make degraded operation observable and ensure fallbacks do not violate security or critical business invariants.

### Q105. How would you find a bottleneck in a system serving millions of requests?

**Answer:** Establish service-level objectives and inspect latency percentiles, throughput, error rates, saturation, and traces. Break the request path into compute, database, cache, network, and downstream dependencies. Use load tests and production telemetry to find the limiting resource, then address it and remeasure. Avoid scaling every component indiscriminately.

## 6. Software Development Best Practices

### Q106. How do you apply SOLID principles?

**Answer:** SOLID encourages one clear responsibility per module, extension without unnecessary modification, substitutable implementations, small client-specific interfaces, and dependency inversion toward abstractions. Apply the principles pragmatically: they should reduce coupling and make changes easier, not create excessive interfaces or indirection.

### Q107. What is the difference between cohesion and coupling?

**Answer:** Cohesion describes how closely related the responsibilities within a module are. Coupling describes how dependent modules are on one another. High cohesion and appropriately low coupling generally improve maintainability and testability. In practice, focus on stable boundaries and clear ownership rather than trying to eliminate all dependencies.

### Q108. How do you decide whether to use a design pattern?

**Answer:** Start with the problem and the simplest design that meets the requirements. Use a pattern when it clarifies a recurring structure or makes change points explicit. Patterns can become overengineering if introduced without a real need. Evaluate readability, testability, flexibility, and the cost of additional abstraction.

### Q109. How do you identify and prioritize refactoring?

**Answer:** Look for duplicated logic, overly complex methods, unclear responsibilities, fragile dependencies, and code that repeatedly causes defects or slows changes. Prioritize refactoring by business risk and change frequency. Add tests around existing behavior, make small changes, and verify results instead of combining broad refactoring with risky feature changes.

### Q110. How do you unit-test code that depends on external systems?

**Answer:** Keep business logic separate from network, database, and filesystem concerns where practical. Use interfaces or well-defined boundaries so unit tests can replace external dependencies with fakes or mocks. Test integration with real infrastructure separately where behavior depends on actual SQL, serialization, or protocol details. Avoid mocking so much that tests only verify implementation details.

### Q111. How do unit, integration, contract, and end-to-end tests differ?

**Answer:** Unit tests validate small units in isolation. Integration tests validate collaboration with real or representative dependencies. Contract tests verify that service providers and consumers agree on an interface. End-to-end tests validate important user or business flows across the deployed system. A balanced test suite uses fast tests frequently and a smaller number of valuable end-to-end tests.

### Q112. How do you test concurrent code?

**Answer:** Identify the concurrency invariant, then use controlled coordination such as latches, barriers, and bounded waits rather than arbitrary sleeps. Run tests repeatedly where useful, inspect state transitions, and use stress or specialized concurrency testing tools when appropriate. Test atomicity and visibility assumptions explicitly; a test passing once does not prove race freedom.

### Q113. What do you look for in a code review?

**Answer:** Review correctness, security, data integrity, error handling, observability, performance risks, tests, and maintainability. Check whether the change fits the existing architecture and whether edge cases are handled. Separate blocking issues from style preferences, explain the reason behind feedback, and keep the review focused on the change's risk and purpose.

### Q114. How do you manage technical debt?

**Answer:** Make debt visible by describing its impact, such as slower delivery, incidents, or increased change risk. Prioritize it alongside feature work using business impact and likelihood of failure. Prefer incremental improvements close to the code being changed, and reserve dedicated work for debt that cannot be addressed safely in small steps.

### Q115. How do you change a database schema safely in production?

**Answer:** Prefer backward-compatible expand-and-contract migrations. First add the new schema in a way that old code can tolerate, then deploy code that supports both forms or gradually migrates data, backfill in batches, and finally remove obsolete structures after verification. Consider locks, defaults, index-build behavior, replication lag, and rollback limitations for large tables.

### Q116. How do feature flags and canary deployments reduce release risk?

**Answer:** Feature flags separate deployment from feature activation and allow controlled rollout or rapid disablement. Canary deployments expose a small portion of traffic to a new version before broader rollout. Monitor error rates, latency, and business metrics, define rollback criteria, and remove obsolete flags to avoid permanent complexity.

### Q117. How do you design logging, metrics, tracing, and alerts?

**Answer:** Use structured logs for diagnostic events, metrics for trends and alerting, and traces for following requests across components. Include correlation identifiers and meaningful service context, but avoid secrets and sensitive payloads. Alert on user-visible symptoms and actionable conditions rather than every internal event. Control metric cardinality and define retention and access policies.

### Q118. How do you manage secrets securely?

**Answer:** Keep credentials, API keys, and private keys out of source control, logs, and client-visible responses. Use a secrets manager or secure deployment mechanism with least privilege, rotation, and access auditing. Avoid sharing long-lived credentials between environments, and have a process for revoking exposed secrets.

### Q119. How do you conduct a production incident review?

**Answer:** First restore service and protect users. Then build a timeline from logs, metrics, traces, deployment events, and operator actions. Identify contributing technical and process factors without assigning personal blame. Create prioritized actions with owners and measurable outcomes, and verify that the corrective actions reduce recurrence or improve detection and recovery.

### Q120. How do you balance clean architecture and delivery speed?

**Answer:** Optimize for the expected lifetime and rate of change of the system, not theoretical purity. Keep boundaries clear where they reduce real coupling, but avoid layers and abstractions that add no value. Make reversible decisions quickly, test risky changes, and invest in architecture where the cost of future change or failure justifies it.
