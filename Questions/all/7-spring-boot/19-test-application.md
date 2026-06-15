# Que - How do you test your application?

## ✅ Interview-ready answer

When I talk about testing a Spring Boot application in interviews, I explain that I use a **layered testing strategy** combining **unit testing, integration testing, and API testing** to ensure the application is reliable and production-ready.

Spring Boot provides strong support through **Spring Test, JUnit 5, Mockito, and TestRestTemplate/WebTestClient**.

---

## 📌 How I explain it in an interview

I test a Spring Boot application at multiple levels:

1. **Unit testing** → testing individual classes (service, utility)
2. **Integration testing** → testing interaction between components (service + repository + DB)
3. **Controller/API testing** → testing REST endpoints

---

# 🧪 1. Unit Testing (Service Layer)

For unit testing, I isolate dependencies using **Mockito**.

```java id="t1"
@ExtendWith(MockitoExtension.class)
class EmployeeServiceTest {

    @Mock
    private EmployeeRepository repository;

    @InjectMocks
    private EmployeeService service;

    @Test
    void testGetEmployeeById() {
        Employee emp = new Employee(1L, "John");

        Mockito.when(repository.findById(1L))
               .thenReturn(Optional.of(emp));

        Employee result = service.getEmployeeById(1L);

        Assertions.assertEquals("John", result.getName());
    }
}
```

### 🔑 Key points:

* Uses Mockito to mock dependencies
* Tests business logic only
* Fast execution

---

# 🔗 2. Integration Testing

Here I test real interaction between layers (Spring context + DB).

```java id="t2"
@SpringBootTest
@TestPropertySource(locations = "classpath:application-test.properties")
class EmployeeRepositoryTest {

    @Autowired
    private EmployeeRepository repository;

    @Test
    void testSaveEmployee() {
        Employee emp = new Employee();
        emp.setName("Alice");

        Employee saved = repository.save(emp);

        Assertions.assertNotNull(saved.getId());
    }
}
```

### 🔑 Key points:

* Loads full Spring context
* Can use H2 in-memory DB for testing
* Slower than unit tests but more realistic

---

# 🌐 3. Controller / API Testing

### Using MockMvc (most common approach)

```java id="t3"
@SpringBootTest
@AutoConfigureMockMvc
class EmployeeControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testGetEmployees() throws Exception {
        mockMvc.perform(get("/employees"))
               .andExpect(status().isOk());
    }
}
```

### Key point:

* Tests REST endpoints without starting server

---

### Alternative: TestRestTemplate (full HTTP test)

```java id="t4"
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class EmployeeApiTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void testGetEmployees() {
        ResponseEntity<String> response =
            restTemplate.getForEntity("/employees", String.class);

        Assertions.assertEquals(200, response.getStatusCodeValue());
    }
}
```

---

# 🧰 Tools used

* **JUnit 5** → testing framework
* **Mockito** → mocking dependencies
* **Spring Boot Test** → integration testing support
* **MockMvc** → controller testing
* **TestRestTemplate / WebTestClient** → API testing
* **H2 database** → in-memory DB for tests

---

## ⭐ Key points interviewers look for

* Knowledge of testing pyramid (unit → integration → API)
* Use of Mockito for isolation
* Use of `@SpringBootTest` for integration testing
* Awareness of MockMvc vs TestRestTemplate
* Understanding of test isolation and repeatability
* Use of in-memory DB for tests

---

## 🚀 Production-level insights

* Maintain separate test config (`application-test.properties`)
* Use H2 or Testcontainers for realistic DB testing
* Avoid using real databases in unit tests
* Keep unit tests fast and independent
* Use CI pipelines to run tests automatically
* Aim for high coverage in service layer (most business logic lives here)
* Prefer Testcontainers in microservices for production-like integration testing

---

## 🔁 Common follow-up questions

1. Difference between unit testing and integration testing?
2. What is Mockito and why do we use it?
3. What is the difference between MockMvc and TestRestTemplate?
4. What are Testcontainers and why are they useful?
5. How do you mock repository layer?
6. How do you ensure test isolation in Spring Boot?

---

If you want next, I can simulate a **real interview deep-dive:**
👉 *“How do you test microservices communication (REST calls between services)?”*
