## 1. Java coding questions - questions only

### A. Strings and arrays

1. Find the first non-repeating character in a string.
2. Find the first repeating character in a string.
3. Find all duplicate characters in a string and their frequencies.
4. Check whether two strings are anagrams without sorting them.
5. Find the longest substring without repeating characters.
6. Find the longest palindromic substring.
7. Find the longest common prefix among an array of strings.
8. Compress a string by replacing consecutive repeated characters with the character followed by its count.
9. Find the smallest window in a string containing all characters of another string.
10. Find the missing number in an array containing numbers from 1 to N.
11. Find all duplicate numbers in an integer array.
12. Find the second-largest distinct number in an array without sorting.
13. Find the maximum sum of a contiguous subarray.
14. Find two numbers in an array whose sum equals a target.
15. Merge two sorted arrays without using an additional array, where feasible.

### B. Collections and data structures

16. Implement an LRU cache with O(1) average-time `get` and `put` operations.
17. Implement a frequency counter that returns the top K most frequent elements.
18. Merge overlapping intervals.
19. Group a list of words into anagram groups.
20. Find the intersection of two arrays, including duplicate-count handling.
21. Implement a stack using queues.
22. Implement a queue using stacks.
23. Find the Kth-largest element in an unsorted array.
24. Detect a cycle in a linked list.
25. Find the intersection point of two linked lists.
26. Reverse a linked list iteratively and recursively.
27. Implement a min stack that supports retrieving the minimum in O(1) time.
28. Design a data structure that supports insert, delete, and retrieving a random element in average O(1) time.

### C. Core Java and object-oriented programming

29. Create an immutable class containing mutable fields and ensure defensive copying.
30. Implement a custom immutable `Employee` class with proper `equals()` and `hashCode()`.
31. Sort employees by department, then salary descending, then name ascending.
32. Implement a custom exception hierarchy for a banking application.
33. Design a thread-safe Singleton and explain how it prevents multiple instances.
34. Implement a generic utility method that finds the maximum element in a collection.
35. Create a custom annotation and use reflection to read it at runtime.
36. Implement a deep-copy mechanism for a nested object structure without using serialization.
37. Design a strategy-based payment processor that supports multiple payment methods without large conditional blocks.
38. Implement a custom LRU cache using `LinkedHashMap`.

### D. Multithreading and concurrency

39. Print odd and even numbers alternately using two threads.
40. Print numbers from 1 to N in sequence using three threads.
41. Implement a producer-consumer solution using `BlockingQueue`.
42. Implement a thread-safe counter using `AtomicInteger`.
43. Implement a thread-safe singleton and explain its initialization guarantees.
44. Demonstrate and prevent a deadlock involving two locks.
45. Implement a bounded blocking queue using `wait()` and `notifyAll()`.
46. Execute multiple independent tasks concurrently and combine their results using `CompletableFuture`.
47. Implement a rate limiter using the token bucket algorithm.
48. Design a thread-safe in-memory cache with expiration and concurrent access.
49. Implement a task executor with a fixed number of worker threads and a bounded queue.
50. Write a program that demonstrates the difference between visibility and atomicity.

### E. Real-world backend coding problems

51. Implement an idempotency mechanism for an order-creation request.
52. Implement a retry utility with exponential backoff and a maximum number of attempts.
53. Implement a circuit breaker with CLOSED, OPEN, and HALF_OPEN states.
54. Build a round-robin load balancer that skips unhealthy servers.
55. Implement weighted round-robin server selection.
56. Process a large file in batches without loading the entire file into memory.
57. Design a notification dispatcher that processes email, SMS, and WhatsApp requests through separate handlers.
58. Implement a deduplication mechanism for incoming events using event IDs.
59. Aggregate transaction amounts by customer while handling invalid records.
60. Design a thread-safe inventory service that prevents stock from becoming negative.
61. Implement a sliding-window rate limiter for API requests.
62. Process paginated API results until all pages have been retrieved, handling transient failures.
63. Implement a bounded in-memory cache with TTL and a maximum entry count.
64. Design a bulk-processing job that can resume safely after a partial failure.
65. Implement a log analyzer that reports the most frequent errors by type and time window.