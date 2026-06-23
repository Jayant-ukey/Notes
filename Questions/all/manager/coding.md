

## 🔤 STRINGS

### 1. Reverse a String Without Built-in Methods

```java
public class ReverseString {
    public static String reverse(String str) {
        char[] chars = str.toCharArray();
        int left = 0, right = chars.length - 1;

        while (left < right) {
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;
            left++;
            right--;
        }
        return new String(chars);
    }

    public static void main(String[] args) {
        System.out.println(reverse("Hello")); // Output: olleH
    }
}
```
**Key Point:** Use two-pointer approach — swap characters from both ends moving inward.

---

### 2. Check if a String is Palindrome

```java
public class Palindrome {
    public static boolean isPalindrome(String str) {
        str = str.toLowerCase();
        int left = 0, right = str.length() - 1;

        while (left < right) {
            if (str.charAt(left) != str.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }

    public static void main(String[] args) {
        System.out.println(isPalindrome("Racecar")); // true
        System.out.println(isPalindrome("Hello"));   // false
    }
}
```
**Key Point:** Compare characters from both ends. If any mismatch → not a palindrome.

---

### 3. Find All Permutations of a String

```java
public class Permutations {
    public static void permute(String str, String result) {
        if (str.isEmpty()) {
            System.out.println(result);
            return;
        }
        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);
            String remaining = str.substring(0, i) + str.substring(i + 1);
            permute(remaining, result + ch);
        }
    }

    public static void main(String[] args) {
        permute("ABC", "");
        // Output: ABC, ACB, BAC, BCA, CAB, CBA
    }
}
```
**Key Point:** Use **recursion** — pick each character one by one and permute the remaining string.

---

## 📦 COLLECTIONS CODING

### 4. Find Duplicate Elements in a List

```java
import java.util.*;

public class FindDuplicates {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 2, 4, 5, 1, 6);

        Set<Integer> seen = new HashSet<>();
        Set<Integer> duplicates = new LinkedHashSet<>();

        for (int num : list) {
            if (!seen.add(num)) {       // add() returns false if already present
                duplicates.add(num);
            }
        }
        System.out.println("Duplicates: " + duplicates); // [2, 1]
    }
}
```
**Key Point:** `HashSet.add()` returns `false` if element already exists — use that to detect duplicates.

---

### 5. Count Frequency of Each Character in a String

```java
import java.util.*;

public class CharFrequency {
    public static void main(String[] args) {
        String str = "programming";

        Map<Character, Integer> freqMap = new LinkedHashMap<>();

        for (char ch : str.toCharArray()) {
            freqMap.put(ch, freqMap.getOrDefault(ch, 0) + 1);
        }

        freqMap.forEach((k, v) -> System.out.println(k + " -> " + v));
        // g -> 2, r -> 2, a -> 1, m -> 2 ...
    }
}
```
**Key Point:** Use `HashMap` with `getOrDefault()` to count occurrences cleanly.

---

### 6. Sort Employee Objects by Salary

```java
import java.util.*;

class Employee {
    String name;
    double salary;

    Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    public String toString() {
        return name + " - " + salary;
    }
}

public class SortEmployee {
    public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("Alice", 75000));
        employees.add(new Employee("Bob", 50000));
        employees.add(new Employee("Charlie", 90000));

        // Sort by salary ascending
        employees.sort(Comparator.comparingDouble(e -> e.salary));

        employees.forEach(System.out::println);
        // Bob - 50000, Alice - 75000, Charlie - 90000
    }
}
```
**Key Point:** Use `Comparator.comparingDouble()` for sorting. For **descending**, add `.reversed()`.

---

### 7. Find Second Highest Number in an Array

```java
public class SecondHighest {
    public static int findSecondHighest(int[] arr) {
        int first = Integer.MIN_VALUE;
        int second = Integer.MIN_VALUE;

        for (int num : arr) {
            if (num > first) {
                second = first;   // old first becomes second
                first = num;
            } else if (num > second && num != first) {
                second = num;
            }
        }
        return second;
    }

    public static void main(String[] args) {
        int[] arr = {10, 5, 20, 8, 15};
        System.out.println("Second Highest: " + findSecondHighest(arr)); // 15
    }
}
```
**Key Point:** Track `first` and `second` in a **single pass O(n)**. Interviewers love this over sorting (O n log n).

---


Here are all the answers with clean, interview-ready code:

---

## ☕ JAVA 8 — STREAMS

### 1. Find Employees Whose Salary > 50000

```java
import java.util.*;
import java.util.stream.*;

class Employee {
    String name;
    String department;
    double salary;

    Employee(String name, String department, double salary) {
        this.name = name;
        this.department = department;
        this.salary = salary;
    }
    public String toString() { return name + " (" + department + ") - " + salary; }
}

public class StreamDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Alice", "IT",      75000),
            new Employee("Bob",   "HR",      45000),
            new Employee("Charlie","IT",     90000),
            new Employee("David", "Finance", 30000)
        );

        List<Employee> highEarners = employees.stream()
            .filter(e -> e.salary > 50000)       // filter condition
            .collect(Collectors.toList());

        highEarners.forEach(System.out::println);
        // Alice (IT) - 75000
        // Charlie (IT) - 90000
    }
}
```
**Key Point:** `filter()` takes a **Predicate** — returns only elements matching the condition.

---

### 2. Group Employees by Department

```java
import java.util.*;
import java.util.stream.*;

public class GroupByDemo {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Alice",   "IT",      75000),
            new Employee("Bob",     "HR",      45000),
            new Employee("Charlie", "IT",      90000),
            new Employee("David",   "Finance", 30000),
            new Employee("Eve",     "HR",      60000)
        );

        Map<String, List<Employee>> grouped = employees.stream()
            .collect(Collectors.groupingBy(e -> e.department));

        grouped.forEach((dept, emps) -> {
            System.out.println(dept + ": " + emps);
        });
        // IT:      [Alice, Charlie]
        // HR:      [Bob, Eve]
        // Finance: [David]
    }
}
```
**Key Point:** `Collectors.groupingBy()` returns a `Map<Key, List<T>>` — very common interview question!

---

### 3. Find Duplicate Numbers Using Streams

```java
import java.util.*;
import java.util.stream.*;

public class DuplicateStream {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 5, 1, 6, 3);

        Set<Integer> seen = new HashSet<>();

        Set<Integer> duplicates = numbers.stream()
            .filter(n -> !seen.add(n))        // add() returns false if already present
            .collect(Collectors.toSet());

        System.out.println("Duplicates: " + duplicates); // [1, 2, 3]
    }
}
```
**Key Point:** `HashSet.add()` returns `false` for duplicates — use it inside `filter()` as a stateful predicate.

---

### 4. Convert List\<String\> to Uppercase Using Streams

```java
import java.util.*;
import java.util.stream.*;

public class UpperCaseStream {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("alice", "bob", "charlie", "david");

        List<String> upperNames = names.stream()
            .map(String::toUpperCase)           // map transforms each element
            .collect(Collectors.toList());

        System.out.println(upperNames);
        // [ALICE, BOB, CHARLIE, DAVID]
    }
}
```
**Key Point:** `map()` transforms each element. `String::toUpperCase` is a **method reference** — cleaner than a lambda.

---

## 🧵 MULTITHREADING

### 1. Create Two Threads (3 Ways — Know All!)

```java
// ✅ Way 1: Extending Thread class
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread 1 running: " + Thread.currentThread().getName());
    }
}

// ✅ Way 2: Implementing Runnable (Preferred)
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Thread 2 running: " + Thread.currentThread().getName());
    }
}

public class TwoThreads {
    public static void main(String[] args) {
        // Way 1
        MyThread t1 = new MyThread();
        t1.start();

        // Way 2
        Thread t2 = new Thread(new MyRunnable());
        t2.start();

        // ✅ Way 3: Lambda (Java 8+)
        Thread t3 = new Thread(() ->
            System.out.println("Thread 3 running: " + Thread.currentThread().getName())
        );
        t3.start();
    }
}
```
**Key Point:** Always prefer `Runnable` over `Thread` — Java allows only single inheritance, so extending Thread blocks future inheritance.

---

### 2. Difference Between Runnable and Callable

```java
import java.util.concurrent.*;

public class RunnableVsCallable {
    public static void main(String[] args) throws Exception {

        // ✅ Runnable — no return value, no checked exception
        Runnable runnable = () -> System.out.println("Runnable executed");
        new Thread(runnable).start();

        // ✅ Callable — returns a value, can throw checked exception
        Callable<Integer> callable = () -> {
            System.out.println("Callable executed");
            return 42;
        };

        ExecutorService executor = Executors.newSingleThreadExecutor();
        Future<Integer> future = executor.submit(callable);

        System.out.println("Callable Result: " + future.get()); // 42
        executor.shutdown();
    }
}
```

| Feature | Runnable | Callable |
|---|---|---|
| Return Value | ❌ void | ✅ Returns value via `Future` |
| Checked Exception | ❌ Cannot throw | ✅ Can throw |
| Method | `run()` | `call()` |
| Used With | `Thread` / `ExecutorService` | `ExecutorService` only |

---

### 3. Print Odd and Even Numbers Using Two Threads

```java
public class OddEvenThreads {
    private static final Object LOCK = new Object();
    private static int number = 1;
    private static final int LIMIT = 10;

    // Even Thread
    static class EvenThread implements Runnable {
        public void run() {
            while (number <= LIMIT) {
                synchronized (LOCK) {
                    if (number % 2 == 0) {
                        System.out.println("Even Thread: " + number++);
                        LOCK.notify();
                    } else {
                        try { LOCK.wait(); } catch (InterruptedException e) { }
                    }
                }
            }
        }
    }

    // Odd Thread
    static class OddThread implements Runnable {
        public void run() {
            while (number <= LIMIT) {
                synchronized (LOCK) {
                    if (number % 2 != 0) {
                        System.out.println("Odd Thread:  " + number++);
                        LOCK.notify();
                    } else {
                        try { LOCK.wait(); } catch (InterruptedException e) { }
                    }
                }
            }
        }
    }

    public static void main(String[] args) {
        new Thread(new OddThread()).start();
        new Thread(new EvenThread()).start();
    }
}
// Output:
// Odd Thread:  1
// Even Thread: 2
// Odd Thread:  3 ... and so on
```
**Key Point:** Use `wait()` and `notify()` inside a `synchronized` block to coordinate between threads.

---

### 4. Producer Consumer Implementation

```java
import java.util.LinkedList;
import java.util.Queue;

public class ProducerConsumer {
    private static final Queue<Integer> queue = new LinkedList<>();
    private static final int CAPACITY = 5;
    private static final Object LOCK = new Object();

    // ✅ Producer
    static class Producer implements Runnable {
        public void run() {
            int value = 1;
            while (true) {
                synchronized (LOCK) {
                    while (queue.size() == CAPACITY) {       // Queue full → wait
                        try { LOCK.wait(); } catch (InterruptedException e) { }
                    }
                    System.out.println("Produced: " + value);
                    queue.add(value++);
                    LOCK.notifyAll();                        // Notify consumer
                }
            }
        }
    }

    // ✅ Consumer
    static class Consumer implements Runnable {
        public void run() {
            while (true) {
                synchronized (LOCK) {
                    while (queue.isEmpty()) {                // Queue empty → wait
                        try { LOCK.wait(); } catch (InterruptedException e) { }
                    }
                    int val = queue.poll();
                    System.out.println("Consumed: " + val);
                    LOCK.notifyAll();                        // Notify producer
                }
            }
        }
    }

    public static void main(String[] args) {
        Thread producer = new Thread(new Producer());
        Thread consumer = new Thread(new Consumer());
        producer.start();
        consumer.start();
    }
}
```
**Key Point:** Always use `while` (not `if`) with `wait()` to guard against **spurious wakeups** — a very common follow-up question!

---


Here are all the SQL answers with clear explanations:

---

## 🗄️ TABLE SETUP (For Reference)

```sql
CREATE TABLE Employee (
    emp_id  INT,
    name    VARCHAR(50),
    salary  DECIMAL(10,2),
    dept    VARCHAR(50)
);

INSERT INTO Employee VALUES
(1, 'Alice',   75000, 'IT'),
(2, 'Bob',     45000, 'HR'),
(3, 'Charlie', 90000, 'IT'),
(4, 'David',   30000, 'Finance'),
(5, 'Eve',     60000, 'HR'),
(6, 'Frank',   45000, 'HR'),      -- duplicate salary in HR
(7, 'Grace',   90000, 'IT');      -- duplicate salary in IT
```

---

## 1️⃣ Find Second Highest Salary

### ✅ Method 1 — Using LIMIT/OFFSET (MySQL)
```sql
SELECT DISTINCT salary
FROM Employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Output: 75000
```

### ✅ Method 2 — Using Subquery (Works everywhere)
```sql
SELECT MAX(salary) AS second_highest
FROM Employee
WHERE salary < (SELECT MAX(salary) FROM Employee);

-- Output: 75000
```

### ✅ Method 3 — Using DENSE_RANK (Best for interviews!)
```sql
SELECT salary
FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM Employee
) ranked
WHERE rnk = 2;

-- Output: 75000
```

| Method | Handles Duplicates | Works On |
|---|---|---|
| LIMIT OFFSET | ✅ with DISTINCT | MySQL |
| Subquery | ✅ with MAX | All DBs |
| DENSE_RANK | ✅ Best approach | All DBs |

> 🔥 **Interviewer Tip:** Always mention `DENSE_RANK()` — it handles duplicates gracefully and works for **Nth highest salary** too. Just change `WHERE rnk = 2` to any N.

---

## 2️⃣ Find Employees Earning More Than Department Average

```sql
SELECT e.emp_id,
       e.name,
       e.salary,
       e.dept,
       dept_avg.avg_salary
FROM Employee e
JOIN (
    SELECT dept,
           AVG(salary) AS avg_salary
    FROM Employee
    GROUP BY dept
) dept_avg
ON e.dept = dept_avg.dept
WHERE e.salary > dept_avg.avg_salary;
```

### Output:
```
emp_id | name    | salary | dept    | avg_salary
-------|---------|--------|---------|------------
1      | Alice   | 75000  | IT      | 85000
3      | Charlie | 90000  | IT      | 85000  ✅ above IT avg
5      | Eve     | 60000  | HR      | 45000  ✅ above HR avg
```

> 🔥 **Interviewer Tip:** You can also use a **correlated subquery**:
```sql
SELECT emp_id, name, salary, dept
FROM Employee e
WHERE salary > (
    SELECT AVG(salary)
    FROM Employee
    WHERE dept = e.dept      -- correlated: refers to outer query
);
```

---

## 3️⃣ Find Duplicate Records

### ✅ Find duplicate salaries
```sql
SELECT salary,
       COUNT(*) AS count
FROM Employee
GROUP BY salary
HAVING COUNT(*) > 1;

-- Output:
-- 45000 → 2 times (Bob, Frank)
-- 90000 → 2 times (Charlie, Grace)
```

### ✅ Show full duplicate rows
```sql
SELECT *
FROM Employee
WHERE salary IN (
    SELECT salary
    FROM Employee
    GROUP BY salary
    HAVING COUNT(*) > 1
)
ORDER BY salary;

-- Output: Bob, Frank (45000) and Charlie, Grace (90000)
```

### ✅ Find exact duplicate rows (all columns match)
```sql
SELECT name, salary, dept,
       COUNT(*) AS count
FROM Employee
GROUP BY name, salary, dept
HAVING COUNT(*) > 1;
```

> 🔥 **Interviewer Tip:** Clarify whether they mean **duplicate salary** or **exact duplicate row** — shows analytical thinking!

---

## 4️⃣ INNER JOIN vs LEFT JOIN

### Setup — Two Tables
```sql
CREATE TABLE Department (
    dept_id   INT,
    dept_name VARCHAR(50)
);

INSERT INTO Department VALUES
(1, 'IT'),
(2, 'HR'),
(3, 'Finance'),
(4, 'Marketing');   -- No employees in Marketing
```

---

### ✅ INNER JOIN — Only Matching Rows
```sql
SELECT e.name, e.salary, d.dept_name
FROM Employee e
INNER JOIN Department d
ON e.dept = d.dept_name;
```
```
name    | salary | dept_name
--------|--------|----------
Alice   | 75000  | IT
Bob     | 45000  | HR
Charlie | 90000  | IT
...
-- ❌ Marketing NOT shown (no employees)
```

---

### ✅ LEFT JOIN — All Left Table Rows + Matching Right
```sql
SELECT e.name, e.salary, d.dept_name
FROM Employee e
LEFT JOIN Department d
ON e.dept = d.dept_name;
```
```
name    | salary | dept_name
--------|--------|----------
Alice   | 75000  | IT
Bob     | 45000  | HR
Charlie | 90000  | IT
...
-- ✅ All employees shown even if dept doesn't match
```

---

### ✅ RIGHT JOIN — Show Departments with No Employees
```sql
SELECT e.name, d.dept_name
FROM Employee e
RIGHT JOIN Department d
ON e.dept = d.dept_name;
```
```
name  | dept_name
------|----------
...
NULL  | Marketing   ✅ Marketing shown with NULL employee
```

---

### 📊 Visual Difference

```
INNER JOIN          LEFT JOIN           RIGHT JOIN
  A  ∩  B             A  +  (A∩B)         B  +  (A∩B)

 [  A  [█████]  B ]  [████████]  B ]  [  A  [████████]
       matched        all of A           all of B
```

| Feature | INNER JOIN | LEFT JOIN | RIGHT JOIN |
|---|---|---|---|
| Matching rows | ✅ | ✅ | ✅ |
| Non-matching LEFT | ❌ | ✅ | ❌ |
| Non-matching RIGHT | ❌ | ❌ | ✅ |
| NULLs returned | ❌ | ✅ Right side | ✅ Left side |

---

## 💡 Final Quick Cheat Sheet

| Question | Key Concept |
|---|---|
| 2nd Highest Salary | `DENSE_RANK()` or `MAX WHERE < MAX` |
| Above Dept Average | Subquery with `AVG + GROUP BY` |
| Duplicate Records | `GROUP BY + HAVING COUNT(*) > 1` |
| INNER JOIN | Only matched rows from both tables |
| LEFT JOIN | All left rows + matched right rows |

> 🔥 **Bonus Tip:** If asked **"Nth highest salary"** use:
```sql
SELECT salary
FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM Employee
) ranked
WHERE rnk = N;   -- Replace N with 2, 3, 4...
```

Let me know if you want **Spring Boot, Hibernate, Design Patterns or System Design** questions covered! 💪
