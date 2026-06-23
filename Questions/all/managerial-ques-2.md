
### What kind of hands-on questions can appear?

#### 1. Live Coding (Most Likely)

These are the types of coding questions repeatedly reported by candidates:

**Strings**

```java
Reverse a string without using built-in methods.
```

```java
Check if a string is palindrome.
```

```java
Find all permutations of a string.
```

A candidate specifically reported a permutation question. ([Reddit][2])

---

#### 2. Collections Coding

```java
Find duplicate elements in a List.
```

```java
Count frequency of each character in a String.
```

```java
Sort Employee objects by salary.
```

```java
Find second highest number in an array.
```

Expected Java 8 solution using Streams.

Example:

```java
int secondHighest = Arrays.stream(arr)
        .boxed()
        .sorted(Collections.reverseOrder())
        .distinct()
        .skip(1)
        .findFirst()
        .get();
```

---

#### 3. Java 8 Hands-On

Very common for 4-year profiles.

```java
Find employees whose salary > 50000 using streams.
```

```java
Group employees by department.
```

```java
Find duplicate numbers using streams.
```

```java
Convert List<String> to uppercase using streams.
```

Example:

```java
list.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

---

#### 4. Multithreading

You may be asked to write code.

```java
Create two threads.
```

```java
Difference between Runnable and Callable.
```

```java
Print odd and even numbers using two threads.
```

```java
Producer Consumer implementation.
```

---

#### 5. Debugging Questions

Very common in managerial rounds.

Example:

```java
HashMap<String,Integer> map = new HashMap<>();

map.put("A",1);
map.put("A",2);

System.out.println(map.size());
```

Questions:

* Output?
* Why?
* How does HashMap work internally?

Or:

```java
String s1 = "abc";
String s2 = new String("abc");

System.out.println(s1 == s2);
System.out.println(s1.equals(s2));
```

---

#### 6. SQL Hands-On

Sometimes the manager asks SQL because every Java developer is expected to know it.

**Employee Table**

| emp_id | name | salary | dept |
| ------ | ---- | ------ | ---- |

Questions:

```sql
Find second highest salary.
```

```sql
Find employees earning more than department average.
```

```sql
Find duplicate records.
```

```sql
Difference between INNER JOIN and LEFT JOIN.
```

---

#### 7. Spring Boot Practical Scenarios

Very common.

**Questions**

* Design CRUD APIs for Employee.
* How do you implement exception handling?
* How do you secure REST APIs?
* How do you implement pagination?
* How do you call another microservice?

Code example they may ask:

```java
@GetMapping("/employees")
public Page<Employee> getEmployees(
        Pageable pageable) {
    return employeeRepo.findAll(pageable);
}
```

---

#### 8. Microservices Scenario-Based Hands-On

These are becoming increasingly common.

* Service A calls Service B and response time is 15 seconds. What will you do?
* How will you implement circuit breaker?
* How will you trace a request across 10 microservices?
* How will you prevent cascading failures?

Candidates report questions around fault tolerance, debugging distributed systems, and microservices design. ([Ace Your Tech Interview][3])

---

### If I were interviewing a Java developer with 4 years at Infosys tomorrow, my top 10 expected hands-on questions would be:

1. Reverse a string.
2. Find duplicate characters in a string.
3. Second highest salary SQL query.
4. Group employees by department using Streams.
5. Explain and code Singleton.
6. Design a REST API for Employee CRUD.
7. Difference between HashMap and ConcurrentHashMap.
8. Write code using CompletableFuture.
9. Explain Kafka consumer flow used in your project.
10. Debug a Java code snippet and identify issues.

### Highest-Risk Question

For a 4-year Java/Spring Boot profile, the one question that catches many candidates is:

> "Open Notepad and implement Employee CRUD APIs with proper layers (Controller, Service, Repository, Entity) and exception handling."
