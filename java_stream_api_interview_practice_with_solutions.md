# Java Stream API Practice Questions with Solutions

These exercises are intended for Java developers preparing for interviews at the 5–10 years of experience level. Most solutions use Java 8 Stream API features. Unless stated otherwise, assume the required imports are present:

```java
import java.util.*;
import java.util.function.*;
import java.util.stream.*;
```

For employee-related questions, assume this model:

```java
class Employee {
    private final int id;
    private final String name;
    private final String department;
    private final double salary;
    private final int age;

    public Employee(int id, String name, String department, double salary, int age) {
        this.id = id;
        this.name = name;
        this.department = department;
        this.salary = salary;
        this.age = age;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getSalary() { return salary; }
    public int getAge() { return age; }

    @Override
    public String toString() {
        return name + " (" + department + ", " + salary + ")";
    }
}
```

## 1. Filter even numbers from a list

**Question:** Given a list of integers, return only the even numbers.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

List<Integer> evenNumbers = numbers.stream()
        .filter(n -> n % 2 == 0)
        .collect(Collectors.toList());

System.out.println(evenNumbers); // [2, 4, 6]
```

**Explanation:** `filter()` keeps elements that satisfy the predicate. The terminal operation `collect()` creates the result list.

## 2. Find the squares of all numbers

**Question:** Convert a list of integers into a list containing their squares.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(2, 3, 4);

List<Integer> squares = numbers.stream()
        .map(n -> n * n)
        .collect(Collectors.toList());

System.out.println(squares); // [4, 9, 16]
```

**Explanation:** `map()` transforms each element into another value.

## 3. Find the sum and average of a list

**Question:** Calculate the sum and average of a list of integers.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(10, 20, 30, 40);

int sum = numbers.stream()
        .mapToInt(Integer::intValue)
        .sum();

OptionalDouble average = numbers.stream()
        .mapToInt(Integer::intValue)
        .average();

System.out.println(sum);     // 100
System.out.println(average); // OptionalDouble[25.0]
```

**Explanation:** Primitive streams such as `IntStream` provide numeric operations and avoid unnecessary boxing for those operations. An average is optional because the input may be empty.

## 4. Find the maximum and minimum number

**Question:** Find the maximum and minimum values in a list of integers.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(12, 5, 34, 8, 2);

Optional<Integer> max = numbers.stream().max(Integer::compareTo);
Optional<Integer> min = numbers.stream().min(Integer::compareTo);

System.out.println(max.orElse(null)); // 34
System.out.println(min.orElse(null)); // 2
```

**Explanation:** `max()` and `min()` return `Optional` because the stream may be empty.

## 5. Find the second-highest distinct number

**Question:** Find the second-highest distinct number in a list of integers.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(10, 20, 30, 30, 40, 40, 50);

Optional<Integer> secondHighest = numbers.stream()
        .distinct()
        .sorted(Comparator.reverseOrder())
        .skip(1)
        .findFirst();

System.out.println(secondHighest.orElse(null)); // 40
```

**Explanation:** `distinct()` removes duplicates, sorting places the largest value first, and `skip(1)` skips the highest. This solution sorts the values, so it is O(n log n) rather than a one-pass solution.

## 6. Find duplicate numbers

**Question:** Find all numbers that appear more than once in a list.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 5, 1, 2);

Set<Integer> seen = new HashSet<>();

Set<Integer> duplicates = numbers.stream()
        .filter(n -> !seen.add(n))
        .collect(Collectors.toSet());

System.out.println(duplicates); // [1, 2] (set display order may vary)
```

**Explanation:** `Set.add()` returns `false` when the value is already present. This is concise, but it uses state inside the stream pipeline; for parallel streams, do not use this pattern. Prefer grouping/counting when a stateless pipeline is required.

## 7. Count the frequency of each number

**Question:** Count how many times each number appears in a list.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3);

Map<Integer, Long> frequency = numbers.stream()
        .collect(Collectors.groupingBy(
                Function.identity(),
                Collectors.counting()));

System.out.println(frequency); // {1=1, 2=2, 3=3}
```

**Explanation:** `Function.identity()` uses each number as its own grouping key, and `counting()` counts elements in each group.

## 8. Find the first non-repeating character

**Question:** Find the first character that occurs exactly once in a string.

**Solution:**

```java
String input = "swiss";

Map<Character, Long> counts = input.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(
                Function.identity(),
                LinkedHashMap::new,
                Collectors.counting()));

Character result = counts.entrySet().stream()
        .filter(entry -> entry.getValue() == 1)
        .map(Map.Entry::getKey)
        .findFirst()
        .orElse(null);

System.out.println(result); // w
```

**Explanation:** `LinkedHashMap` preserves first-seen character order. This example operates on UTF-16 `char` values; for full Unicode code-point handling, use `codePoints()` and work with integers.

## 9. Count the frequency of each word

**Question:** Count the frequency of each word in a sentence, ignoring case and basic punctuation.

**Solution:**

```java
String sentence = "Java is great, and Java is popular!";

Map<String, Long> wordCounts = Arrays.stream(
                sentence.toLowerCase(Locale.ROOT)
                        .replaceAll("[^a-z0-9\\s]", "")
                        .split("\\s+"))
        .filter(word -> !word.isEmpty())
        .collect(Collectors.groupingBy(
                Function.identity(),
                Collectors.counting()));

System.out.println(wordCounts);
```

**Explanation:** This is a simple English-text example. Production tokenization may need to account for Unicode, apostrophes, hyphens, and language-specific rules.

## 10. Sort strings by length

**Question:** Sort a list of strings by length, shortest first.

**Solution:**

```java
List<String> words = Arrays.asList("Spring", "Java", "Microservices", "API");

List<String> sorted = words.stream()
        .sorted(Comparator.comparingInt(String::length))
        .collect(Collectors.toList());

System.out.println(sorted); // [API, Java, Spring, Microservices]
```

**Explanation:** `Comparator.comparingInt()` creates a comparator based on the string length.

## 11. Join strings with a delimiter

**Question:** Join a list of names into a single comma-separated string.

**Solution:**

```java
List<String> names = Arrays.asList("Asha", "Ravi", "Neha");

String result = names.stream()
        .collect(Collectors.joining(", "));

System.out.println(result); // Asha, Ravi, Neha
```

**Explanation:** `joining()` combines character sequences and can also accept a prefix and suffix.

## 12. Flatten a list of lists

**Question:** Convert a nested list into a single list.

**Solution:**

```java
List<List<Integer>> nested = Arrays.asList(
        Arrays.asList(1, 2),
        Arrays.asList(3, 4),
        Arrays.asList(5, 6));

List<Integer> flattened = nested.stream()
        .flatMap(List::stream)
        .collect(Collectors.toList());

System.out.println(flattened); // [1, 2, 3, 4, 5, 6]
```

**Explanation:** `map()` would produce a stream of lists. `flatMap()` maps each list to a stream and flattens the results into one stream.

## 13. Find common elements between two lists

**Question:** Find the distinct elements that appear in both lists.

**Solution:**

```java
List<Integer> first = Arrays.asList(1, 2, 3, 4, 4);
List<Integer> second = Arrays.asList(3, 4, 4, 5, 6);

Set<Integer> secondSet = new HashSet<>(second);

List<Integer> common = first.stream()
        .filter(secondSet::contains)
        .distinct()
        .collect(Collectors.toList());

System.out.println(common); // [3, 4]
```

**Explanation:** Building a set for membership checks is generally more efficient than repeatedly scanning the second list.

## 14. Partition numbers into even and odd

**Question:** Split a list of integers into even and odd numbers.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

Map<Boolean, List<Integer>> partitioned = numbers.stream()
        .collect(Collectors.partitioningBy(n -> n % 2 == 0));

System.out.println(partitioned.get(true));  // [2, 4, 6]
System.out.println(partitioned.get(false)); // [1, 3, 5]
```

**Explanation:** `partitioningBy()` always groups values into the two Boolean categories.

## 15. Find the top three largest distinct numbers

**Question:** Return the three largest distinct numbers in descending order.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(5, 12, 9, 12, 20, 15, 20, 7);

List<Integer> topThree = numbers.stream()
        .distinct()
        .sorted(Comparator.reverseOrder())
        .limit(3)
        .collect(Collectors.toList());

System.out.println(topThree); // [20, 15, 12]
```

**Explanation:** This is easy to read and suitable for moderate input sizes. For very large streams, a bounded priority queue may be more efficient.

## 16. Group employees by department

**Question:** Group employees by their department.

**Solution:**

```java
Map<String, List<Employee>> byDepartment = employees.stream()
        .collect(Collectors.groupingBy(Employee::getDepartment));
```

**Explanation:** `groupingBy()` creates one list per department. The result map's iteration order is not guaranteed by the default collector.

## 17. Count employees in each department

**Question:** Count employees department-wise.

**Solution:**

```java
Map<String, Long> countByDepartment = employees.stream()
        .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.counting()));
```

**Explanation:** A downstream collector performs the count for each group without first needing a separate list per department.

## 18. Find the highest-paid employee

**Question:** Find the employee with the highest salary.

**Solution:**

```java
Optional<Employee> highestPaid = employees.stream()
        .max(Comparator.comparingDouble(Employee::getSalary));

highestPaid.ifPresent(System.out::println);
```

**Explanation:** `max()` returns an `Optional` because the employee list may be empty.

## 19. Find the highest-paid employee in each department

**Question:** Return the highest-paid employee for every department.

**Solution:**

```java
Map<String, Optional<Employee>> highestPaidByDepartment =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.maxBy(
                                Comparator.comparingDouble(Employee::getSalary))));
```

**Explanation:** `maxBy()` is a downstream collector. Each department maps to an `Optional<Employee>` because a maximum may be absent for an empty group, though groups created from this stream are non-empty.

## 20. Calculate average salary by department

**Question:** Calculate the average salary in each department.

**Solution:**

```java
Map<String, Double> averageSalaryByDepartment = employees.stream()
        .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.averagingDouble(Employee::getSalary)));
```

**Explanation:** `averagingDouble()` computes a `Double` average for each department.

## 21. Find the second-highest salary

**Question:** Find the second-highest distinct employee salary.

**Solution:**

```java
Optional<Double> secondHighestSalary = employees.stream()
        .map(Employee::getSalary)
        .distinct()
        .sorted(Comparator.reverseOrder())
        .skip(1)
        .findFirst();

System.out.println(secondHighestSalary.orElse(null));
```

**Explanation:** Distinct salaries are sorted in descending order, and the highest is skipped. If fewer than two distinct salaries exist, the result is empty.

## 22. Find the second-highest-paid employee in each department

**Question:** Return the second-highest distinct salary employee for each department.

**Solution:**

```java
Map<String, Optional<Employee>> secondHighestByDepartment =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.collectingAndThen(
                                Collectors.toList(),
                                list -> list.stream()
                                        .sorted(Comparator.comparingDouble(
                                                Employee::getSalary).reversed())
                                        .collect(Collectors.collectingAndThen(
                                                Collectors.toList(),
                                                sorted -> {
                                                    if (sorted.isEmpty()) {
                                                        return Optional.<Employee>empty();
                                                    }
                                                    double highest = sorted.get(0).getSalary();
                                                    return sorted.stream()
                                                            .filter(e -> e.getSalary() < highest)
                                                            .max(Comparator.comparingDouble(
                                                                    Employee::getSalary));
                                                }))));
```

**Explanation:** The solution excludes the top salary and then finds the maximum among the remaining employees. Employees tied at the highest salary are all excluded from the next distinct salary. For production use, consider a clearer helper method or grouping salary levels before selecting the second level.

## 23. Sort employees by salary descending, then name ascending

**Question:** Sort employees by salary from highest to lowest, using name alphabetically to break ties.

**Solution:**

```java
List<Employee> sortedEmployees = employees.stream()
        .sorted(Comparator.comparingDouble(Employee::getSalary)
                .reversed()
                .thenComparing(Employee::getName))
        .collect(Collectors.toList());
```

**Explanation:** `thenComparing()` adds a tie-breaker. The name comparator is ascending even though salary is descending.

## 24. Convert a list of employees into a map keyed by employee ID

**Question:** Create a map where employee ID is the key and the employee is the value.

**Solution:**

```java
Map<Integer, Employee> employeeById = employees.stream()
        .collect(Collectors.toMap(
                Employee::getId,
                Function.identity()));
```

**Explanation:** This throws `IllegalStateException` if duplicate IDs occur. If duplicates are possible, define a merge function rather than silently assuming uniqueness.

Example with a merge policy:

```java
Map<Integer, Employee> employeeById = employees.stream()
        .collect(Collectors.toMap(
                Employee::getId,
                Function.identity(),
                (existing, replacement) -> existing));
```

## 25. Find duplicate employee names

**Question:** Find names that occur more than once, ignoring case.

**Solution:**

```java
Set<String> duplicateNames = employees.stream()
        .map(e -> e.getName().toLowerCase(Locale.ROOT))
        .collect(Collectors.groupingBy(
                Function.identity(),
                Collectors.counting()))
        .entrySet()
        .stream()
        .filter(entry -> entry.getValue() > 1)
        .map(Map.Entry::getKey)
        .collect(Collectors.toSet());
```

**Explanation:** Normalize the names before grouping so that values such as `Ravi` and `RAVI` are treated as the same name.

## 26. Find the department with the most employees

**Question:** Find the department with the largest employee count.

**Solution:**

```java
Optional<Map.Entry<String, Long>> departmentWithMostEmployees =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.counting()))
                .entrySet()
                .stream()
                .max(Map.Entry.comparingByValue());

departmentWithMostEmployees.ifPresent(System.out::println);
```

**Explanation:** First count employees by department, then find the entry with the largest count. If multiple departments tie, this returns one of the maximum entries.

## 27. Find employees older than 30 and earn above a salary threshold

**Question:** Return employees older than 30 whose salary is greater than 100,000.

**Solution:**

```java
List<Employee> result = employees.stream()
        .filter(e -> e.getAge() > 30)
        .filter(e -> e.getSalary() > 100_000)
        .collect(Collectors.toList());
```

**Explanation:** Chained filters make each condition explicit. The threshold is illustrative; use the currency and salary scale applicable to the application.

## 28. Get the names of the three highest-paid employees

**Question:** Return the names of the three employees with the highest salaries.

**Solution:**

```java
List<String> topThreeNames = employees.stream()
        .sorted(Comparator.comparingDouble(Employee::getSalary).reversed())
        .limit(3)
        .map(Employee::getName)
        .collect(Collectors.toList());
```

**Explanation:** This returns up to three employees, not three distinct salary levels. Add a deterministic tie-breaker if stable ordering among equal salaries matters.

## 29. Check whether all employees earn at least a minimum salary

**Question:** Check whether every employee earns at least 30,000.

**Solution:**

```java
boolean allMeetMinimum = employees.stream()
        .allMatch(e -> e.getSalary() >= 30_000);
```

**Explanation:** `allMatch()` short-circuits when it finds a failing element. It returns `true` for an empty stream, which is the mathematical definition of universal quantification; decide whether an empty employee list should be handled separately.

## 30. Check whether any employee belongs to a given department

**Question:** Check whether at least one employee belongs to the IT department.

**Solution:**

```java
boolean hasITEmployee = employees.stream()
        .anyMatch(e -> "IT".equalsIgnoreCase(e.getDepartment()));
```

**Explanation:** `anyMatch()` short-circuits as soon as a match is found. Putting the constant string first avoids a null dereference if the department field is null.

## 31. Find the first employee whose name starts with a given prefix

**Question:** Find the first employee whose name starts with `A`.

**Solution:**

```java
Optional<Employee> firstEmployee = employees.stream()
        .filter(e -> e.getName() != null && e.getName().startsWith("A"))
        .findFirst();
```

**Explanation:** `findFirst()` respects encounter order when the stream has one. Use `findAny()` when any matching element is acceptable, particularly in parallel processing.

## 32. Partition employees into salary bands

**Question:** Partition employees into those earning at least 100,000 and those earning less.

**Solution:**

```java
Map<Boolean, List<Employee>> salaryBands = employees.stream()
        .collect(Collectors.partitioningBy(
                e -> e.getSalary() >= 100_000));

List<Employee> higherPaid = salaryBands.get(true);
List<Employee> lowerPaid = salaryBands.get(false);
```

**Explanation:** `partitioningBy()` splits a stream into two groups according to a predicate.

## 33. Flatten a list of employees' skills and find unique skills

**Question:** Given a list of employees, each with a list of skills, produce a list of distinct skills. Assume `getSkills()` returns `List<String>`.

**Solution:**

```java
List<String> uniqueSkills = employees.stream()
        .flatMap(e -> e.getSkills().stream())
        .filter(Objects::nonNull)
        .distinct()
        .sorted()
        .collect(Collectors.toList());
```

**Explanation:** `flatMap()` combines all employee skill streams into one stream. This assumes `getSkills()` itself is non-null; guard against null lists if the model permits them.

## 34. Use reduce to multiply all numbers

**Question:** Calculate the product of all integers in a list using `reduce()`.

**Solution:**

```java
List<Integer> numbers = Arrays.asList(2, 3, 4);

int product = numbers.stream()
        .reduce(1, (a, b) -> a * b);

System.out.println(product); // 24
```

**Explanation:** The identity value `1` is the multiplicative identity. With an empty stream, the result is 1. Be aware that integer overflow is possible for large products.

## 35. Find the total salary using mapToDouble

**Question:** Calculate the total salary of all employees.

**Solution:**

```java
double totalSalary = employees.stream()
        .mapToDouble(Employee::getSalary)
        .sum();
```

**Explanation:** `mapToDouble()` creates a `DoubleStream`, which provides numeric aggregation operations.

## 36. Find the most frequent character in a string

**Question:** Find the character that appears most frequently in a string.

**Solution:**

```java
String input = "programming";

Optional<Map.Entry<Character, Long>> mostFrequent = input.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(
                Function.identity(),
                Collectors.counting()))
        .entrySet()
        .stream()
        .max(Map.Entry.comparingByValue());

mostFrequent.ifPresent(System.out::println);
```

**Explanation:** This counts UTF-16 `char` values and returns one maximum entry. If there is a tie, the selected character is not guaranteed. Use `LinkedHashMap` if we need first-seen ordering as a tie-breaker.

## 37. Find the longest string in a list

**Question:** Find the longest string in a list.

**Solution:**

```java
List<String> words = Arrays.asList("Java", "Spring Boot", "Microservices");

Optional<String> longest = words.stream()
        .filter(Objects::nonNull)
        .max(Comparator.comparingInt(String::length));

System.out.println(longest.orElse(null)); // Microservices
```

**Explanation:** `max()` compares string lengths and returns an `Optional`. If multiple strings share the maximum length, one is returned according to the comparator and encounter behavior.

## 38. Skip null values and collect non-empty strings

**Question:** Remove null and blank strings from a list.

**Solution (Java 11+):**

```java
List<String> cleaned = values.stream()
        .filter(Objects::nonNull)
        .map(String::trim)
        .filter(s -> !s.isEmpty())
        .collect(Collectors.toList());
```

**Explanation:** This version works in Java 8 too because `trim()` and `isEmpty()` exist there. Java 11's `String.isBlank()` can be used if whitespace-only detection should follow its Unicode-aware semantics.

## 39. Group words by their first character

**Question:** Group words by their first character, ignoring case.

**Solution:**

```java
Map<Character, List<String>> grouped = words.stream()
        .filter(Objects::nonNull)
        .filter(s -> !s.isEmpty())
        .collect(Collectors.groupingBy(
                s -> Character.toLowerCase(s.charAt(0))));
```

**Explanation:** The classifier determines the map key. This uses the first UTF-16 `char`; for supplementary Unicode characters, use code points instead.

## 40. Understand lazy evaluation and short-circuiting

**Question:** Given a stream pipeline with `filter()`, `map()`, and `findFirst()`, explain which operations execute and why.

**Solution:**

```java
Optional<Integer> result = Stream.of(1, 2, 3, 4, 5)
        .filter(n -> {
            System.out.println("filter: " + n);
            return n % 2 == 0;
        })
        .map(n -> {
            System.out.println("map: " + n);
            return n * 10;
        })
        .findFirst();

System.out.println(result.orElse(null)); // 20
```

**Explanation:** Intermediate operations such as `filter()` and `map()` are lazy. The terminal operation `findFirst()` triggers evaluation. Processing stops once the first matching element has passed through the pipeline, so the stream does not need to process every element.

## 41. Why should side effects be avoided in stream pipelines?

**Question:** Explain the risk of mutating shared state inside a stream operation.

**Answer:** Stream pipelines are easiest to reason about when functions are stateless and non-interfering. Mutating a shared collection or counter inside `map()` or `forEach()` can introduce race conditions, ordering surprises, and incorrect results, especially with parallel streams. Prefer collectors such as `groupingBy()`, `counting()`, and `toMap()` for reductions. If a side effect is required, define its concurrency and ordering requirements explicitly.

## 42. When should we avoid parallel streams?

**Question:** Give examples where parallel streams may be slower or unsafe.

**Answer:** Avoid assuming parallel execution is faster for small inputs, blocking I/O, highly dependent operations, shared mutable state, or tasks with expensive splitting and merging. Parallel streams commonly use the shared ForkJoinPool unless another mechanism is deliberately used. Benchmark representative workloads and check thread-safety, ordering, resource limits, and interactions with other work.

## 43. What is the difference between map() and flatMap()?

**Question:** Explain the difference between `map()` and `flatMap()` with an example.

**Answer:** `map()` transforms each element into one result. `flatMap()` transforms each element into a stream-like result and flattens those results into a single stream.

```java
List<List<String>> nested = Arrays.asList(
        Arrays.asList("a", "b"),
        Arrays.asList("c", "d"));

List<Stream<String>> mapped = nested.stream()
        .map(List::stream)
        .collect(Collectors.toList());

List<String> flattened = nested.stream()
        .flatMap(List::stream)
        .collect(Collectors.toList());

System.out.println(flattened); // [a, b, c, d]
```

## 44. What is the difference between findFirst() and findAny()?

**Question:** Explain their behavior and when to use each.

**Answer:** `findFirst()` returns the first element according to encounter order when one exists. `findAny()` may return any matching element and can provide more flexibility for parallel streams. Both return an `Optional` and short-circuit the pipeline.

## 45. What is the difference between groupingBy() and partitioningBy()?

**Question:** Explain when to use each collector.

**Answer:** `groupingBy()` groups elements by a classifier and can produce many keys, such as one group per department. `partitioningBy()` divides elements into two Boolean groups according to a predicate. Use grouping for categories and partitioning for yes/no classifications.

## Practice recommendations

- First solve each question without looking at the solution.
- Explain the pipeline operation used and why it fits the problem.
- Consider empty input, duplicates, nulls, ordering, and tie-breaking.
- Know the difference between intermediate and terminal operations.
- Avoid side effects in stream lambdas, especially with parallel streams.
- Practice writing equivalent imperative code and compare readability and complexity.
- Confirm the Java version before using newer APIs; the examples are primarily Java 8 compatible.
