# Que - How can you ensure 100% test coverage in a spring boot application?

## ✅ Interview-ready answer

In practice, **I don’t aim for “100% test coverage” as the main goal**, but I ensure **maximum meaningful coverage with high business confidence**. However, if the question is specifically about achieving 100% coverage, I explain it from both **technical and practical perspective**.

---

## 📌 How I explain it in an interview

To ensure high or near 100% test coverage in a Spring Boot application, I combine:

* Unit tests (business logic)
* Integration tests (Spring context + DB)
* Controller/API tests (endpoints)
* Code coverage tools (JaCoCo)

And I enforce coverage metrics in the build pipeline.

---

# 🧪 1. Use Code Coverage Tools (JaCoCo)

I use **JaCoCo plugin** to measure and enforce coverage.

```xml id="c1"
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.11</version>
    <executions>
        <execution>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>test</phase>
            <goals>
                <goal>report</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### Key point:

* Generates HTML report showing line/branch coverage
* Helps identify untested code paths

---

# 📊 2. Enforce coverage threshold (CI/CD)

I configure a **minimum coverage rule**, so builds fail if coverage is low:

```xml id="c2"
<configuration>
    <rules>
        <rule>
            <element>PACKAGE</element>
            <limits>
                <limit>
                    <counter>LINE</counter>
                    <value>COVEREDRATIO</value>
                    <minimum>0.85</minimum>
                </limit>
            </limits>
        </rule>
    </rules>
</configuration>
```

---

# 🧪 3. Write layered tests properly

### ✔ Unit tests (highest coverage impact)

* Service layer logic
* Utility classes
* Business rules

### ✔ Integration tests

* Repository + DB
* Spring context loading

### ✔ Controller tests

* API endpoints
* Request/response validation

---

# 🧠 4. Cover all code paths

To reach high coverage, I ensure:

* Positive scenarios (happy path)
* Negative scenarios (exceptions)
* Boundary conditions
* Null/empty input cases

Example:

```java id="c3"
@Test
void testEmployeeNotFound() {
    Mockito.when(repository.findById(1L))
           .thenReturn(Optional.empty());

    Assertions.assertThrows(ResourceNotFoundException.class,
        () -> service.getEmployeeById(1L));
}
```

---

# 🚀 5. Use mocking properly

* Mock external dependencies (DB, APIs)
* Avoid missing branches due to unmocked conditions
* Use Mockito `when/thenReturn` for all scenarios

---

# ⚠️ Important real-world insight (very important in interviews)

I also clarify this:

👉 **100% test coverage does NOT mean bug-free code**

Because:

* It only measures executed lines, not correctness
* Doesn’t guarantee business scenario coverage
* Doesn’t test real-world system behavior

So I focus on:
✔ Critical business logic coverage
✔ Risk-based testing
✔ Integration stability

---

# ⭐ Key points interviewers look for

* Knowledge of **JaCoCo or coverage tools**
* Understanding of **unit vs integration coverage**
* Ability to design tests for all branches
* Awareness that 100% coverage ≠ high quality
* Use of CI/CD enforcement for coverage thresholds
* Handling positive + negative + edge cases

---

# 🚀 Production-level insights

* Focus more on **critical modules (payment, auth, orders)** than full coverage
* Use **branch coverage**, not just line coverage
* Combine coverage tools with **mutation testing (advanced teams)**
* Use CI pipelines (Jenkins/GitHub Actions) to enforce coverage gates
* Avoid writing tests just to increase coverage numbers

---

# 🔁 Common follow-up questions

1. What is the difference between line coverage and branch coverage?
2. How does JaCoCo work internally?
3. Is 100% test coverage required in real projects?
4. What is mutation testing?
5. How do you decide what to test in a service layer?
6. How do you improve coverage in legacy code?

---

If you want next, I can take you to a **senior-level testing question:**
👉 *“How do you test microservices that depend on external services (REST calls, Kafka, etc.)?”*
