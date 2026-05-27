# What are Indexes in SQL?

An **index** in SQL is a database object used to improve the speed of data retrieval operations on a table.

It works similar to an index in a book:

* Without index → database scans entire table
* With index → database directly locates required rows faster

Indexes are mainly used to optimize:

* `SELECT`
* `WHERE`
* `JOIN`
* `ORDER BY`
* `GROUP BY`

queries.

---

# Example

Suppose we have an employee table:

```sql id="fvdj2u"
EMPLOYEE
---------
id
name
email
department
```

If we frequently search by email:

```sql id="j4pw1n"
SELECT * FROM employee WHERE email = 'abc@test.com';
```

Without index:

* Full table scan happens

With index:

```sql id="w3k0eo"
CREATE INDEX idx_employee_email
ON employee(email);
```

Database can directly locate the row much faster.

---

# How Index Works Internally

Most relational databases use:

* **B-Tree (Balanced Tree)** structure internally.

B-Tree allows:

* Fast searching
* Fast insertion
* Sorted traversal

Time complexity improves roughly from:

```text id="glr3o7"
O(n) → O(log n)
```

---

# Types of Indexes

This is important in interviews.

---

# 1. Primary Index / Clustered Index

Created automatically on primary key in many databases.

Data is physically stored in sorted order based on clustered index.

Example:

```sql id="kq75lf"
PRIMARY KEY(id)
```

Only one clustered index per table.

---

# 2. Non-Clustered Index

Stores index separately from actual table data.

Contains:

* Indexed column
* Pointer/reference to actual row

Example:

```sql id="v4hhrm"
CREATE INDEX idx_name
ON employee(name);
```

Multiple non-clustered indexes allowed.

---

# 3. Unique Index

Ensures duplicate values are not allowed.

```sql id="xjlwm9"
CREATE UNIQUE INDEX idx_email
ON employee(email);
```

---

# 4. Composite Index

Index created on multiple columns.

```sql id="tycx9p"
CREATE INDEX idx_dept_salary
ON employee(department, salary);
```

Useful when queries filter on multiple columns together.

---

# 5. Full-Text Index

Used for searching large text content efficiently.

Common in:

* Search engines
* Product search
* Document systems

---

# Advantages of Indexes

## Faster Query Performance

Main advantage.

Especially useful for:

* Large tables
* Frequently searched columns

---

## Faster JOIN Operations

Indexes on foreign keys improve join performance.

---

## Faster Sorting and Grouping

Helps `ORDER BY` and `GROUP BY`.

---

# Disadvantages / Trade-offs

Very important for experienced-level interviews.

---

## Increased Storage

Indexes require additional disk space.

---

## Slower INSERT/UPDATE/DELETE

Whenever data changes:

* Index also needs updating

Too many indexes reduce write performance.

---

## Maintenance Overhead

Unused indexes waste resources.

---

# When Should We Create Indexes?

Good candidates explain practical scenarios.

Create indexes on:

* Frequently searched columns
* WHERE conditions
* JOIN columns
* Foreign keys
* ORDER BY columns
* Large tables

Avoid indexing:

* Small tables
* Frequently updated columns
* Low cardinality columns sometimes (like gender/status)

---

# What is Cardinality?

Interviewers may ask this follow-up.

Cardinality means uniqueness of data.

Example:

| Column | Cardinality |
| ------ | ----------- |
| email  | High        |
| gender | Low         |

High-cardinality columns are better candidates for indexes.

---

# How to Check Query Performance?

Using:

```sql id="mepj6j"
EXPLAIN ANALYZE
```

This shows:

* Index scan
* Sequential scan
* Cost
* Execution plan

Common in PostgreSQL and MySQL.

---

# Real Project-Level Answer

You can say:

> “In our project, we created indexes mainly on frequently queried columns like userId, orderId, and transaction status. We also used composite indexes for reporting queries. During performance optimization, we used EXPLAIN ANALYZE to identify full table scans and improve query execution time.”

This sounds practical and experienced.

---

# Common Follow-up Questions

## Difference between Clustered and Non-Clustered Index?

| Clustered             | Non-Clustered           |
| --------------------- | ----------------------- |
| Physically sorts data | Separate structure      |
| Only one per table    | Multiple allowed        |
| Faster range queries  | Faster specific lookups |

---

## Why too many indexes are bad?

Because every insert/update/delete must also update indexes.

---

## What is covering index?

When all required columns are available in the index itself, reducing table lookup.

---

# Short Crisp Interview Version (2-minute Answer)

> An index in SQL is a database object used to improve query performance by enabling faster data retrieval. Internally, most databases use B-Tree structures for indexing.
>
> Indexes are mainly useful for WHERE clauses, JOINs, ORDER BY, and GROUP BY operations.
>
> Common types include clustered, non-clustered, unique, and composite indexes.
>
> While indexes improve read performance significantly, they also increase storage usage and slow down insert/update/delete operations because indexes must be maintained.
>
> In production, we typically create indexes on frequently searched columns and use EXPLAIN ANALYZE to optimize queries.
