# Que - What is the difference between @RequestParam, @PathVariable, and @RequestBody?

## ✅ Interview-ready answer

In Spring Boot REST APIs, `@RequestParam`, `@PathVariable`, and `@RequestBody` are used to extract data from an HTTP request, but they serve **different purposes depending on where the data is coming from**.

---

## 📌 How I explain it in an interview

* `@PathVariable` → extracts values from the **URL path**
* `@RequestParam` → extracts values from the **query parameters**
* `@RequestBody` → extracts data from the **request body (usually JSON)**

---

# 🔹 1. @PathVariable (URL path parameter)

Used when the value is part of the URL structure and represents a specific resource.

### Example

```java id="p1"
@GetMapping("/employees/{id}")
public Employee getEmployee(@PathVariable Long id) {
    return service.getEmployee(id);
}
```

### Request:

```
GET /employees/101
```

👉 Here `101` is part of the path.

---

## 🧠 Key point:

* Used for identifying **specific resources**
* Required in RESTful design
* Cannot be optional in most cases

---

# 🔹 2. @RequestParam (Query parameter)

Used for filtering, searching, or optional parameters.

### Example

```java id="p2"
@GetMapping("/employees")
public List<Employee> getEmployees(@RequestParam String department) {
    return service.getByDepartment(department);
}
```

### Request:

```
GET /employees?department=IT
```

---

## 🧠 Key point:

* Used for **filters, sorting, pagination**
* Optional parameters possible
* Can have default values

```java id="p3"
@RequestParam(defaultValue = "0") int page
```

---

# 🔹 3. @RequestBody (Request payload)

Used to receive **complex objects (usually JSON)** from request body.

### Example

```java id="p4"
@PostMapping("/employees")
public Employee createEmployee(@RequestBody Employee employee) {
    return service.save(employee);
}
```

### Request:

```json id="p5"
{
  "name": "John",
  "department": "IT"
}
```

---

## 🧠 Key point:

* Used for **POST, PUT, PATCH**
* Maps JSON → Java object automatically
* Used for complex data structures

---

# 📊 Quick comparison

| Annotation      | Source       | Usage             | Example              |
| --------------- | ------------ | ----------------- | -------------------- |
| `@PathVariable` | URL path     | Identify resource | `/employees/1`       |
| `@RequestParam` | Query string | Filters, search   | `/employees?dept=IT` |
| `@RequestBody`  | Request body | Send object/data  | JSON payload         |

---

# 🚀 Real-world usage together

We often use all three in one API:

```java id="p6"
@PutMapping("/employees/{id}")
public Employee updateEmployee(
        @PathVariable Long id,
        @RequestParam(required = false) String source,
        @RequestBody Employee employee) {

    return service.update(id, employee);
}
```

---

# ⭐ Key points interviewers look for

* `@PathVariable` → resource identification
* `@RequestParam` → filtering/querying
* `@RequestBody` → sending structured data
* JSON mapping happens via Jackson in `@RequestBody`
* REST best practice: use each correctly based on intent

---

# 🚀 Production-level insights

* Use `@RequestParam` for pagination:

  * `page`, `size`, `sort`
* Use `@PathVariable` for resource identity:

  * `/users/{id}`
* Avoid overusing query params for mandatory fields
* Validate `@RequestBody` using `@Valid`
* Always design APIs following REST conventions for clarity

---

# 🔁 Common follow-up questions

1. Can `@RequestParam` be optional?
2. What happens if `@PathVariable` is missing?
3. How does Spring convert JSON in `@RequestBody`?
4. Difference between `@RequestParam` and `@QueryParam` (JAX-RS)?
5. Can we use multiple `@PathVariable` in one URL?
6. What happens if request body is invalid?

---

If you want next, I can give you a **very common interview trick question:**
👉 *“Can we use @RequestBody with GET request? Why or why not?”*
