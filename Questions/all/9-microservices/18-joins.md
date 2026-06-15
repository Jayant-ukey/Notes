# Que - What are the different types of joins available in databases?

### ✅ Interview-Ready Answer (Types of Joins in Databases)

Joins in SQL are used to **combine rows from two or more tables based on a related column**, usually a primary key–foreign key relationship. In real-world Spring Boot applications, joins are very important because most data is normalized across multiple tables.

---

# 🔹 1. INNER JOIN

An **INNER JOIN** returns only the matching records from both tables.

👉 If there is no match, the row is excluded.

### Example:

```sql id="jn1"
SELECT e.id, e.name, d.dept_name
FROM employee e
INNER JOIN department d
ON e.dept_id = d.id;
```

✔ Only employees who have a valid department will be returned.

---

# 🔹 2. LEFT JOIN (LEFT OUTER JOIN)

A **LEFT JOIN** returns:

* All records from the left table
* Matching records from the right table
* NULL if no match exists

### Example:

```sql id="jn2"
SELECT e.name, d.dept_name
FROM employee e
LEFT JOIN department d
ON e.dept_id = d.id;
```

✔ All employees will be returned, even if they don’t have a department.

---

# 🔹 3. RIGHT JOIN (RIGHT OUTER JOIN)

A **RIGHT JOIN** returns:

* All records from the right table
* Matching records from the left table
* NULL if no match exists

### Example:

```sql id="jn3"
SELECT e.name, d.dept_name
FROM employee e
RIGHT JOIN department d
ON e.dept_id = d.id;
```

✔ All departments will be returned even if no employees exist in them.

---

# 🔹 4. FULL OUTER JOIN

A **FULL OUTER JOIN** returns:

* All records from both tables
* Matched and unmatched rows
* NULL where no match exists

### Example:

```sql id="jn4"
SELECT e.name, d.dept_name
FROM employee e
FULL OUTER JOIN department d
ON e.dept_id = d.id;
```

✔ Combines LEFT + RIGHT JOIN

---

# 🔹 5. CROSS JOIN

A **CROSS JOIN** returns:

👉 Cartesian product (every row of table A × every row of table B)

### Example:

```sql id="jn5"
SELECT e.name, d.dept_name
FROM employee e
CROSS JOIN department d;
```

✔ Used rarely (mostly for combinations or testing scenarios)

---

# 🔹 6. SELF JOIN

A **SELF JOIN** is when a table joins with itself.

### Example use case:

Employee hierarchy (manager–employee relationship)

```sql id="jn6"
SELECT e1.name AS Employee, e2.name AS Manager
FROM employee e1
JOIN employee e2
ON e1.manager_id = e2.id;
```

---

# 🔹 7. Real-World Microservices Insight (Very Important)

In microservices architecture:

👉 We usually avoid joins across services because:

* Each service has its own database
* Cross-service joins are not allowed

Instead, we use:

* API calls
* Event-driven data duplication (Kafka)
* Denormalized data models

✔ This is a key architectural point interviewers like.

---

# 📌 Key Points Interviewers Look For

* INNER JOIN → only matching records
* LEFT JOIN → all from left table
* RIGHT JOIN → all from right table
* FULL OUTER JOIN → all records from both tables
* CROSS JOIN → Cartesian product
* SELF JOIN → same table relationship
* Awareness that joins are avoided across microservices databases

---

# ⚠️ Common Follow-up Questions

* Difference between INNER JOIN and LEFT JOIN?
* What is a Cartesian product?
* When do you use SELF JOIN in real applications?
* Can we use joins in microservices architecture?
* What is the performance impact of joins?
* How does indexing affect join performance?

---

# 🧾 Short Answer (40–50 seconds)

“In databases, joins are used to combine data from multiple tables based on a related column. The main types are INNER JOIN, which returns only matching records; LEFT JOIN, which returns all records from the left table and matching ones from the right; RIGHT JOIN, which returns all records from the right table; and FULL OUTER JOIN, which returns all records from both tables. We also have CROSS JOIN, which produces a Cartesian product, and SELF JOIN, where a table joins with itself. In microservices, we generally avoid cross-service joins because each service has its own database, and instead we use APIs or event-driven communication to combine data.”

---

If you want next, I can ask a **very strong senior-level question**:
👉 “Why are joins discouraged in microservices architecture, and what are the alternatives?”
