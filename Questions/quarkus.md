## Part 1: Fundamentals

### 1. What is Quarkus and why use it?

**Short answer:** Quarkus is a cloud-native, Kubernetes-friendly Java framework designed for fast startup, low memory usage, and a great developer experience. It works on the JVM and can also compile to a native executable with GraalVM.

**Details:**
- **Fast startup and low memory (RSS):** ideal for containers, microservices, serverless, and autoscaling, where instances start and stop often.
- **Developer joy:** live reload (dev mode), Dev UI, Dev Services (auto-started databases, brokers), and continuous testing.
- **Standards-based:** uses Jakarta REST, CDI, JPA (Hibernate ORM), MicroProfile (Config, Health, Fault Tolerance, OpenAPI, and so on).
- **Imperative and reactive:** you can mix blocking and non-blocking code (Vert.x and Mutiny underneath).
- **Rich extension ecosystem:** Hibernate/Panache, Kafka, gRPC, OIDC, Kubernetes, and more.

**Typical use cases:** microservices, REST APIs, event-driven apps, serverless functions, and Kubernetes workloads.

---

### 2. Quarkus vs Spring Boot

**Short answer:** Both are mature frameworks for building Java services. The key difference is **when the framework does its work**. Quarkus moves as much as possible (classpath scanning, dependency injection wiring, config processing) to **build time**. Spring Boot does much more of it at **runtime**, using reflection and proxies.

| Aspect | Quarkus | Spring Boot |
|---|---|---|
| Framework processing | Mostly at **build time** | Mostly at **runtime** (reflection, proxies) |
| Startup and memory | Faster startup, lower memory, especially in native mode | Heavier on JVM; improved with Spring AOT and native support |
| Native image | First-class design goal; extensions are built to be native-friendly | Supported via Spring AOT and GraalVM, but it came later |
| Dev experience | Live reload, Dev UI, Dev Services, continuous testing | Devtools restart, Testcontainers support, big tooling ecosystem |
| DI | ArC (CDI Lite) | Spring IoC container |
| Standards | Jakarta EE and MicroProfile | Spring's own programming model |
| Ecosystem and community | Smaller, growing fast | Huge, very mature |
| Reactive | Built on Vert.x and Mutiny | Spring WebFlux and Reactor |

**How to answer in an interview:** don't say one is "better." A balanced line is:

> "Quarkus shines when startup time, memory footprint, and container density matter, such as serverless or Kubernetes at scale. Spring Boot has a bigger ecosystem and more available talent. If a team already knows Spring, the migration cost is a real factor. Quarkus also offers Spring compatibility extensions to ease it."

---

### 3. Do you need GraalVM?

**Short answer:** **No.** Quarkus runs on a regular JDK in **JVM mode**. GraalVM (or Mandrel, Red Hat's distribution) is needed **only if you want a native executable**.

**Details:**
- Normal `mvn package` produces a runnable JAR (JVM mode) with no GraalVM involved.
- Native build: `./mvnw package -Dnative`.
- If GraalVM is not installed locally, you can build natively inside a container:
  `./mvnw package -Dnative -Dquarkus.native.container-build=true`

---

### 4. Why is Quarkus startup so fast?

**Short answer:** Because Quarkus does the heavy lifting at **build time** instead of at startup.

**Details:**
- **Build-time processing:** annotation scanning, dependency injection graph resolution, config parsing, and metadata generation all happen during the build.
- **Less reflection and runtime proxies:** ArC generates bytecode at build time rather than discovering and wiring beans at runtime.
- **Dead-code elimination:** unused classes and paths can be dropped, which matters especially in native mode.
- **Native mode:** GraalVM ahead-of-time compilation removes JVM warm-up and class-loading overhead, so the app starts almost instantly.
- **Smaller footprint:** less work at boot means fewer classes loaded and lower memory.

> "A traditional app boots by *discovering* what it needs. A Quarkus app boots already *knowing* what it needs, because the build step worked it out."

---

### 5. What are the two runtime modes?

**Short answer:** **JVM mode** and **native mode**.

| | JVM mode | Native mode |
|---|---|---|
| Output | Runnable JAR (`quarkus-app/quarkus-run.jar`) | Platform-specific native binary |
| Needs | JDK at runtime | No JVM at runtime |
| Startup | Fast | Near instant |
| Memory | Low | Even lower |
| Build time | Fast | Slower (compilation takes much longer) |
| Peak throughput | Usually better for long-running workloads (JIT) | Can be lower for long-running, CPU-heavy workloads |
| Limitations | Few | Reflection, dynamic proxies, and resources need registration/configuration |

**When to choose which:**
- **Native:** serverless, CLI tools, short-lived or frequently scaled workloads.
- **JVM:** long-running services where JIT optimization pays off, or when native constraints are inconvenient.

---

### 6. What are Quarkus extensions?

**Short answer:** Extensions are modules that plug a framework or capability into Quarkus. Each is built to work at build time and to be native-friendly.

**Details:**
- They integrate libraries such as Hibernate ORM, Kafka, gRPC, RESTEasy Reactive, and OIDC.
- An extension has a **runtime** part (code that runs in your app) and a **deployment** part (build-time processing: scanning, bytecode generation, and registering native-image metadata).
- Manage them from the CLI or Maven:

```bash
quarkus extension list
quarkus extension add hibernate-orm-panache

# Maven alternative
./mvnw quarkus:add-extension -Dextensions="hibernate-orm-panache"
```

- You can also build **custom extensions** to integrate your own libraries.

---

### 7. How do you create a Quarkus project?

**Short answer:** Three common ways: the **Quarkus CLI**, the **Maven plugin**, or the **web generator at code.quarkus.io**.

**1. Quarkus CLI**
```bash
quarkus create app com.example:my-app --extension='rest,hibernate-orm-panache'
```

**2. Maven plugin**
```bash
mvn io.quarkus.platform:quarkus-maven-plugin:<version>:create \
    -DprojectGroupId=com.example \
    -DprojectArtifactId=my-app \
    -Dextensions='rest'
```

**3. Web generator:** go to **https://code.quarkus.io**, pick the build tool (Maven or Gradle), Java version, and extensions, then download the zip.

Also worth mentioning: your IDE (IntelliJ, VS Code) has Quarkus plugins that wrap the same generator.

---

### 8. What is dev mode?

**Short answer:** Dev mode (`quarkus dev`) runs your app with **live coding**. You change code, save, and the next request triggers an automatic recompile and reload, with no manual rebuild or restart.

```bash
quarkus dev
# or
./mvnw quarkus:dev
```

**Features to mention:**
- **Live reload** for Java code, config, and resources.
- **Dev UI** at `http://localhost:8080/q/dev-ui` to inspect beans, config, endpoints, and extensions.
- **Dev Services:** Quarkus automatically starts containers (for example PostgreSQL, Kafka, Keycloak) for dev and test when no config is provided. This requires a container runtime such as Docker or Podman.
- **Continuous testing:** press `r` in the terminal to toggle tests that re-run on every change.
- **Remote dev mode** is also available for containerized environments.

---

## Part 2: Dependency Injection (CDI / ArC)

### 9. Which DI does Quarkus use?

**Short answer:** Quarkus uses **ArC**, its own build-time dependency injection container based on **Jakarta CDI (Contexts and Dependency Injection)**. It implements a subset of the spec (CDI Lite) and is optimized for build-time processing.

**Details:**
- You use standard annotations: `@Inject`, `@ApplicationScoped`, `@Produces`, `@Qualifier`, and so on.
- Bean discovery, injection points, and proxies are resolved at **build time**.
- Because it's CDI Lite, some full-CDI features are not supported, such as portable extensions. Quarkus provides its own build-time extension mechanism instead.
- Unused beans can be removed automatically (unused bean removal), which reduces footprint.

```java
@ApplicationScoped
public class GreetingService {
    public String greet(String name) {
        return "Hello " + name;
    }
}

@Path("/hello")
public class GreetingResource {

    @Inject
    GreetingService service;

    @GET
    public String hello() {
        return service.greet("Quarkus");
    }
}
```

**Tip:** Quarkus recommends **package-private** fields for injection, because `private` injection requires reflection. A single constructor doesn't even need `@Inject`.

---

### 10. What is a bean, and what does "container-managed" mean?

**Short answer:** A **bean** is an object whose lifecycle (creation, injection, scope, destruction) is **managed by the DI container**, not created manually with `new`. "Container-managed" means the container decides when to create the instance, injects its dependencies, and disposes of it.

**Details:**
- The container finds beans via annotations (a bean-defining annotation such as a scope) or via **producer methods and fields**.
- You declare what you need with `@Inject`, and the container supplies the right instance.
- Benefits: loose coupling, easier testing (you can swap or mock implementations), lifecycle callbacks (`@PostConstruct`, `@PreDestroy`), and cross-cutting features such as interceptors.

```java
@ApplicationScoped
public class MyService {

    @PostConstruct
    void init() { /* container calls this after creation */ }
}

// Producer method: the container manages the returned object
@ApplicationScoped
public class Producers {
    @Produces
    @ApplicationScoped
    ObjectMapper mapper() {
        return new ObjectMapper();
    }
}
```

**Contrast:** `new MyService()` is *not* a bean. No injection happens, and no scope or interceptors apply.

---

### 11. What scopes are available, and what is a qualifier?

#### Scopes

**Short answer:** A scope defines **how long a bean instance lives and how it is shared**.

| Scope | Behavior |
|---|---|
| `@ApplicationScoped` | **One shared instance** for the whole application, created **lazily** on first use. Accessed through a **client proxy**. |
| `@Singleton` | One shared instance, **no client proxy** (slightly faster). Also created lazily unless you use `@Startup`. |
| `@RequestScoped` | **One instance per HTTP request** (or per request context). |
| `@Dependent` | **Default scope.** A **new instance per injection point**. Its lifecycle is tied to the bean that injects it. |
| `@SessionScoped` | One instance per HTTP session (requires the servlet/Undertow extension). |
| `@TransactionScoped` | Tied to the current transaction (added by the narayana-jta extension). |

**Notes:**
- Extensions can add scopes; `@TransactionScoped` is one example.
- To create a bean eagerly at startup, use `@Startup`.
- `@ApplicationScoped` beans must be proxyable (non-final class, and a no-args constructor, which Quarkus can generate automatically).
- **Common interview point:** `@ApplicationScoped` vs `@Singleton`. The first uses a proxy, which gives lazy initialization and works with mocking and interception. The second is a direct reference without a proxy.

#### Qualifiers

**Short answer:** A **qualifier** disambiguates between multiple beans of the **same type**.

```java
@Qualifier
@Retention(RUNTIME)
@Target({METHOD, FIELD, PARAMETER, TYPE})
public @interface Premium {}

public interface PaymentService { void pay(); }

@ApplicationScoped
@Premium
public class PremiumPaymentService implements PaymentService { ... }

@ApplicationScoped
public class BasicPaymentService implements PaymentService { ... }

// Injection
@Inject
@Premium
PaymentService paymentService;
```

**Built-in qualifiers:**
- `@Default`: applied when no qualifier is specified.
- `@Named`: select a bean by name.
- `@Any`: matches all beans of the type (used with `Instance<T>`).

**Related tools:** `@Alternative` (swap implementations), `Instance<T>` (programmatic lookup), `@LookupIfProperty` and `@IfBuildProfile` (conditional beans).

---

### 12. How is Quarkus DI different from Spring DI?

**Short answer:** The biggest difference is **build-time resolution (Quarkus ArC) versus runtime reflection-based resolution (Spring)**. The programming models also differ: CDI is a Jakarta standard, while Spring has its own annotations.

| Aspect | Quarkus (ArC / CDI Lite) | Spring IoC |
|---|---|---|
| Wiring | Resolved at **build time** | Mostly at **runtime** (improved by Spring AOT) |
| Reflection | Minimized; generates bytecode | Heavier use of reflection and proxies |
| Standard | **Jakarta CDI** | Spring's own model |
| Injection annotation | `@Inject` | `@Autowired` (or `@Inject`) |
| Bean definition | Scope annotations, `@Produces` | `@Component`, `@Service`, `@Bean` |
| Default scope | `@Dependent` | **Singleton** |
| Disambiguation | Qualifiers (`@Named`, custom) | `@Qualifier`, `@Primary` |
| Unused bean handling | Removed automatically at build time | Generally kept |
| Missing dependencies | Detected at **build time** | Often detected at **startup/runtime** |

**Strong closing point:**

> "Because ArC validates the injection graph at build time, an unsatisfied or ambiguous dependency fails the **build**, not production at startup. That also makes the app friendlier to native compilation, since there is less reflection to configure."

**Bonus:** Quarkus offers `quarkus-spring-di`, `quarkus-spring-web`, and `quarkus-spring-data-jpa` compatibility extensions, which help teams migrate Spring code.

---

## Part 3: Data, Configuration and APIs

### 13. What is Panache?

**Short answer:** Panache is a Quarkus layer on top of **Hibernate ORM** that removes JPA boilerplate. It supports two styles: **Active Record** (the entity has the query methods) and **Repository** (a separate class holds them).

**Dependency:** `quarkus-hibernate-orm-panache` (plus a JDBC driver extension).

#### Active Record style

The entity extends `PanacheEntity` (auto-generated `Long id`) or `PanacheEntityBase` (you define your own id).

```java
@Entity
public class Person extends PanacheEntity {
    public String name;
    public LocalDate birth;

    public static List<Person> findByName(String name) {
        return list("name", name);
    }
}

// Usage
@Transactional
void create() {
    Person p = new Person();
    p.name = "Alice";
    p.persist();
}

List<Person> all   = Person.listAll();
Person one         = Person.findById(1L);
long total         = Person.count();
List<Person> page  = Person.find("name", "Alice").page(Page.of(0, 20)).list();
```

#### Repository style

```java
@ApplicationScoped
public class PersonRepository implements PanacheRepository<Person> {
    public List<Person> findByName(String name) {
        return list("name", name);
    }
}

@Inject
PersonRepository repo;
```

(Use `PanacheRepositoryBase<Entity, ID>` if the id type is not `Long`.)

#### Active Record vs Repository

| | Active Record | Repository |
|---|---|---|
| Where queries live | On the entity (static methods) | In a separate bean |
| Boilerplate | Least | A bit more |
| Testing/mocking | Harder (static methods) | Easy (inject a mock) |
| Familiar to | Rails/Django-style developers | Spring Data / DDD developers |
| Public fields | Allowed (Panache generates accessors at build time) | Same |

**Good points to add:**
- Simplified queries: `find("name", name)` or `find("name = ?1 and status = ?2", ...)` with no full JPQL needed.
- Built-in paging (`Page.of`), sorting (`Sort.by`), and projection (`.project(Dto.class)`).
- Write operations need `@Transactional` (or a `@TestTransaction` in tests).
- There is a **reactive** variant (`quarkus-hibernate-reactive-panache`) that returns `Uni<T>`.
- Panache doesn't replace Hibernate. You can still use `EntityManager` and the full JPA API whenever needed.

---

### 14. Configuration and profiles

**Short answer:** Quarkus reads config from `application.properties` (MicroProfile Config / SmallRye Config) and supports **profiles** via a `%profile.` prefix. Values can be overridden by system properties and environment variables.

**Built-in profiles:** `dev` (during `quarkus dev`), `test` (during tests), `prod` (default for packaged apps).

```properties
# Default for all profiles
greeting.message=Hello

# Profile-specific
%dev.quarkus.datasource.jdbc.url=jdbc:postgresql://localhost:5432/devdb
%test.quarkus.datasource.jdbc.url=jdbc:h2:mem:test
%prod.quarkus.datasource.jdbc.url=${DB_URL}

# Expression with default value
app.timeout=${TIMEOUT:30}
```

**Custom profile:** `java -Dquarkus.profile=staging -jar quarkus-run.jar`, or set the environment variable `QUARKUS_PROFILE=staging`. Then use `%staging.some.property=...`.

**Environment variables:** property names are converted to upper case with non-alphanumeric characters replaced by `_`:

```
quarkus.datasource.jdbc.url  ->  QUARKUS_DATASOURCE_JDBC_URL
```

**Precedence (highest to lowest):**
1. System properties (`-Dkey=value`)
2. Environment variables
3. `.env` file in the working directory
4. `application.properties` (in `config/` or on the classpath)
5. `META-INF/microprofile-config.properties`

**Injecting config:**

```java
// Single value
@ConfigProperty(name = "greeting.message", defaultValue = "Hi")
String message;

// Type-safe group of properties (preferred for many related values)
@ConfigMapping(prefix = "greeting")
public interface GreetingConfig {
    String message();
    Optional<String> suffix();
}
```

**Good practices:**
- Never commit secrets. Use env vars, Kubernetes Secrets/ConfigMaps, or a vault extension.
- Build-time vs runtime properties: some properties (for example which extensions or features are enabled) are **fixed at build time** and can't change afterward. Runtime properties can be overridden at startup. The config reference marks each one.
- Use YAML if you prefer: add the `quarkus-config-yaml` extension.

---

### 15. How do you call external REST APIs? (REST Client)

**Short answer:** Use the **Quarkus REST Client**. You declare a Java **interface** annotated with Jakarta REST annotations, and Quarkus **generates the implementation at build time**.

**Dependency:** `quarkus-rest-client` (with `quarkus-rest-client-jackson` for JSON).

```java
@Path("/countries")
@RegisterRestClient(configKey = "country-api")
public interface CountryClient {

    @GET
    @Path("/{code}")
    Country byCode(@PathParam("code") String code);

    // Non-blocking variant
    @GET
    Uni<List<Country>> all();
}
```

```properties
quarkus.rest-client.country-api.url=https://api.example.com
quarkus.rest-client.country-api.read-timeout=5000
```

```java
@Path("/info")
public class InfoResource {

    @Inject
    @RestClient
    CountryClient client;

    @GET
    public Country info() {
        return client.byCode("IN");
    }
}
```

**Why this is good:**
- No manual `HttpClient` code, and no runtime proxy generation.
- Type-safe, and works in native mode.
- Supports `Uni`/`Multi` return types, headers (`@ClientHeaderParam`), and request/response filters.
- Combine with **SmallRye Fault Tolerance** (`@Retry`, `@Timeout`, `@CircuitBreaker`, `@Fallback`) for resilience.

---

### 16. Swagger/OpenAPI and health checks

#### OpenAPI / Swagger UI

**Short answer:** Add **SmallRye OpenAPI** (`quarkus-smallrye-openapi`). It generates the OpenAPI document from your JAX-RS/Jakarta REST code automatically.

- Spec endpoint: `/q/openapi`
- Swagger UI: `/q/swagger-ui` (enabled by default in dev and test; set `quarkus.swagger-ui.always-include=true` to include it in prod)
- Enrich the docs with MicroProfile OpenAPI annotations:

```java
@GET
@Operation(summary = "Get a user", description = "Returns a user by id")
@APIResponse(responseCode = "200", description = "User found")
@APIResponse(responseCode = "404", description = "User not found")
public User get(@PathParam("id") Long id) { ... }
```

#### Health checks

**Short answer:** Add **SmallRye Health** (`quarkus-smallrye-health`). It exposes endpoints for liveness, readiness and startup probes, which Kubernetes uses.

| Endpoint | Purpose |
|---|---|
| `/q/health` | Overall health |
| `/q/health/live` | **Liveness**: is the app running? (restart if not) |
| `/q/health/ready` | **Readiness**: can it take traffic? (remove from load balancer if not) |
| `/q/health/started` | **Startup**: has it finished starting? |

```java
@Readiness
@ApplicationScoped
public class DatabaseHealthCheck implements HealthCheck {

    @Override
    public HealthCheckResponse call() {
        boolean up = checkDatabase();
        return up
            ? HealthCheckResponse.up("database")
            : HealthCheckResponse.down("database");
    }
}
```

Extensions such as the datasource and Kafka contribute health checks automatically. Related: `quarkus-micrometer` for metrics, which is often mentioned alongside health.

---

### 17. What is RESTEasy Reactive, and what is Mutiny?

#### RESTEasy Reactive (now "Quarkus REST")

**Short answer:** It is Quarkus's REST implementation (Jakarta REST) built on **Vert.x** and designed for the reactive core of Quarkus. It does more work at **build time** than classic RESTEasy and supports both **blocking and non-blocking** endpoints. In recent versions the extension is simply called `quarkus-rest`.

**Threading model (a favorite interview topic):**
- Endpoint returns a plain type (`T`, `List<T>`): by default it runs on a **worker thread** (blocking is OK).
- Endpoint returns `Uni<T>`, `Multi<T>` or `CompletionStage<T>`: by default it runs on the **event loop (IO) thread**, so you must **never block** there.
- Override with `@Blocking`, `@NonBlocking`, or `@RunOnVirtualThread` (Java 21+).

#### Mutiny

**Short answer:** Mutiny is the **reactive programming library** Quarkus uses. It has two main types:

| Type | Emits | Think of it as |
|---|---|---|
| `Uni<T>` | **0 or 1** item (or a failure) | An async single result (like a `CompletableFuture`, but lazy) |
| `Multi<T>` | **0 to N** items (or a failure) | An async stream |

```java
@GET
@Path("/{id}")
public Uni<User> get(@PathParam("id") Long id) {
    return userRepo.findById(id)                       // Uni<User>
        .onItem().ifNull().failWith(NotFoundException::new)
        .onItem().transform(u -> enrich(u))
        .onFailure().recoverWithItem(User.anonymous());
}

@GET
@Produces(MediaType.SERVER_SENT_EVENTS)
public Multi<String> ticks() {
    return Multi.createFrom().ticks().every(Duration.ofSeconds(1))
                .map(t -> "tick " + t);
}
```

#### Why developers get confused by `Uni<T>` instead of `T`

- A `Uni<T>` is **not** the value. It's a **description of a future computation** that produces the value.
- It is **lazy**: nothing happens until something **subscribes**. When you return it from a Quarkus endpoint, **the framework subscribes for you**. If you call a method returning `Uni` and ignore the result, nothing runs.
- You transform results by **chaining** (`onItem().transform(...)`, `onItem().transformToUni(...)`), not by "getting the value out."
- **Don't call `.await().indefinitely()` on an event-loop thread.** It blocks, and Quarkus will throw an exception to protect you. Blocking calls belong on a worker thread.

**When to use reactive:** high concurrency with I/O-bound work, streaming, or reactive clients (Kafka, reactive SQL). For simple CRUD, plain blocking endpoints are fine and simpler to read.

---

## Part 4: Testing and Dev Services

### 18. How do you test a Quarkus application?

**Short answer:** Quarkus uses **JUnit 5**. Annotate the test with **`@QuarkusTest`**, which boots the real application, and use **RestAssured** to call endpoints.

**Dependencies:** `quarkus-junit5` and `rest-assured` (test scope).

```java
@QuarkusTest
class GreetingResourceTest {

    @Test
    void helloEndpoint() {
        given()
          .when().get("/hello")
          .then()
             .statusCode(200)
             .body(is("Hello"));
    }
}
```

**Key annotations and tools:**

| Tool | Purpose |
|---|---|
| `@QuarkusTest` | Starts the app once and shares it across test classes. CDI injection works in the test itself. |
| `@QuarkusIntegrationTest` | Tests the **packaged** artifact (JAR, native executable or container) as a black box. No injection into the test. |
| `@TestHTTPEndpoint(Resource.class)` | Sets the base path from the resource class so you don't repeat the URL. |
| `@InjectMock` (`quarkus-junit5-mockito`) | Replaces a CDI bean (such as an `@ApplicationScoped` service) with a Mockito mock. |
| `@TestProfile` | Runs a test with a custom config/profile. |
| `@QuarkusTestResource` | Starts and stops external resources (for example WireMock). |
| `@TestTransaction` | Runs a test inside a transaction that is rolled back afterward. |

```java
@QuarkusTest
class OrderResourceTest {

    @InjectMock
    PaymentService paymentService;   // mocked bean

    @Test
    void createsOrder() {
        Mockito.when(paymentService.charge(any())).thenReturn(true);

        given().contentType(ContentType.JSON)
               .body("{\"item\":\"book\"}")
               .when().post("/orders")
               .then().statusCode(201);
    }
}
```

**Extra points:**
- **Continuous testing** in dev mode re-runs affected tests on each change (`r` to toggle).
- **Dev Services** provide a real database or broker during tests, with no manual setup.
- Native testing: run `@QuarkusIntegrationTest` against a native build (`-Dnative`) to verify native behavior.
- Test pyramid: fast unit tests (plain JUnit/Mockito, no Quarkus boot) for logic, `@QuarkusTest` for integration, and `@QuarkusIntegrationTest` for end-to-end.

---

### 19. What are Dev Services?

**Short answer:** Dev Services **automatically start the backing services your app needs** (databases, brokers, identity providers) in **dev and test mode**, using containers. Quarkus manages their lifecycle and wires the connection settings for you, so you need zero config.

**How it works:**
- You add an extension (for example `quarkus-jdbc-postgresql`) and **don't configure** a URL.
- Quarkus detects this, starts a **PostgreSQL container** (via Testcontainers), and injects the connection details automatically.
- The container stops when the app stops. In dev mode containers can be **reused** between restarts.
- **Requirements:** a container runtime (Docker or Podman).

**Commonly supported services:** PostgreSQL, MySQL, MariaDB, Kafka, Redis, MongoDB, RabbitMQ, Keycloak (OIDC), Elasticsearch/OpenSearch, and more depending on the extension.

**Control and customization:**

```properties
# Disable globally
quarkus.devservices.enabled=false

# Disable or customize one service
quarkus.datasource.devservices.enabled=false
quarkus.datasource.devservices.image-name=postgres:16
quarkus.datasource.devservices.port=5433
```

**Important detail for interviews:**
- Dev Services are **only for dev and test**. They are never used in production, so you still configure real services under `%prod.` (for example `%prod.quarkus.datasource.jdbc.url=...`).
- If you **do** set a connection URL explicitly, Quarkus assumes you want your own service and does not start a container.

**Benefits:**
- Faster onboarding: `git clone`, `quarkus dev`, and everything runs.
- Dev and test environments that match production technology (a real Postgres instead of H2).
- No leftover manual setup scripts or shared dev databases.

---

## Part 5: Quarkus Internals

### 20. Lifecycle of a Quarkus build, and what happens when you run `quarkus dev`

**Short answer:** A Quarkus build has an extra step beyond compiling: **augmentation**. During augmentation, Quarkus analyzes your code and dependencies and does the framework work (bean wiring, config, metadata) up front. At startup the app only **replays** the recorded result, which is why it boots fast.

#### Build lifecycle (production build)

1. **Compile:** Maven/Gradle compiles your sources as usual.
2. **Index:** Quarkus builds a **Jandex index** (a fast, pre-computed annotation and class index) of your application and its dependencies. This replaces runtime classpath scanning.
3. **Augmentation:** the **build steps** from every extension on the classpath run. They consume and produce **BuildItems**, scan the index, resolve the CDI graph, generate and transform bytecode, and **record** what must happen at startup.
4. **Output:** a runnable application, by default `target/quarkus-app/` containing `quarkus-run.jar`, plus `lib/`, `app/` and `quarkus/` folders.
5. **Native only:** the augmented output is passed to GraalVM `native-image`, which compiles it ahead of time into an executable.

#### What happens at startup

Recorded bytecode runs in two phases:
- **Static init:** one-time initialization that does not depend on runtime configuration. In native mode this can already be executed **during the native build**, so its results are baked into the executable.
- **Runtime init:** initialization that needs runtime config (ports, URLs, secrets), such as starting the HTTP server and connecting to services.

**Config note:** properties are either **build-time fixed** (changing them requires a rebuild) or **runtime** (overridable at startup). This split follows directly from the augmentation model.

#### What `quarkus dev` does

```bash
quarkus dev          # or ./mvnw quarkus:dev
```

1. Starts the app in **dev mode** with the full augmentation machinery available (the deployment classpath is included).
2. Runs augmentation **in the same process** and starts the app. No JAR is built.
3. Starts **Dev Services** (containers for databases, brokers, and so on) and the **Dev UI** (`/q/dev-ui`).
4. Uses **two classloaders**:
   - a **base classloader** for dependencies, which stays loaded;
   - a **reloadable classloader** for your application classes, which is thrown away and recreated on change.
5. **Live reload:** when a request arrives (or you trigger it), Quarkus checks for changed sources or resources. If anything changed, it recompiles, re-runs the needed augmentation, and restarts the application in a new classloader. This usually takes well under a second.
6. Opens a **debug port** (5005 by default), and supports **continuous testing** (press `r`).

**One-line summary for the interviewer:**

> "Production builds do augmentation once and ship the result. Dev mode runs augmentation inside a long-lived process and re-does it incrementally on every change, and that's what makes live coding possible."

---

### 21. What is a BuildItem, and how do extensions work internally?

**Short answer:** A **BuildItem** is a typed piece of data passed between **build steps** at build time. Extensions are made of build steps that **consume** and **produce** build items. Quarkus connects the steps into a dependency graph and runs them, in parallel where possible.

#### Extension structure

| Module | Role |
|---|---|
| **Runtime** | Code that ships inside your running app (APIs, recorders, runtime config). |
| **Deployment** | Build-time code only: scans the index, produces build items, generates bytecode, registers native-image metadata. Not shipped in the final app. |
| Integration tests / docs | Optional, but typical for real extensions. |

#### Build steps and BuildItems

A **build step** is a method annotated with `@BuildStep` in the extension's deployment module. It declares what it needs by its **parameters** (consumed items) and what it provides by its **return type** (produced items).

```java
class MyExtensionProcessor {

    @BuildStep
    FeatureBuildItem feature() {
        return new FeatureBuildItem("my-extension");        // shows in startup log
    }

    @BuildStep
    AdditionalBeanBuildItem registerBeans() {
        return AdditionalBeanBuildItem.unremovableOf(MyService.class);   // add a CDI bean
    }

    @BuildStep
    ReflectiveClassBuildItem reflection() {
        return ReflectiveClassBuildItem.builder(MyDto.class)
                   .methods().fields().build();              // native-image reflection config
    }

    @BuildStep
    void scan(CombinedIndexBuildItem index,                  // consumes the Jandex index
              BuildProducer<GeneratedClassBuildItem> generated) {
        // find annotated classes in the index and generate classes
    }
}
```

**Kinds of build items:**
- **SimpleBuildItem:** at most one instance per build.
- **MultiBuildItem:** many instances can be produced and consumed together.
- **EmptyBuildItem:** a marker used only for ordering.

**Common examples:** `CombinedIndexBuildItem`, `AdditionalBeanBuildItem`, `ReflectiveClassBuildItem`, `GeneratedClassBuildItem`, `FeatureBuildItem`.

#### How they connect

- Steps form a **directed acyclic graph**: step B runs after step A if B consumes something A produces.
- Quarkus runs independent steps **in parallel**, which keeps builds fast.
- Extensions don't call each other directly. They cooperate **through build items**, so each stays loosely coupled.

#### Recorders: from build time to runtime

Some build steps need to make something happen **at startup** (for example "start this server" or "register these routes"). They use a **recorder**:

```java
@BuildStep
@Record(ExecutionTime.RUNTIME_INIT)
void startThing(MyRecorder recorder, MyConfig config) {
    recorder.initialize(config);        // not executed now; it is recorded as bytecode
}
```

The recorder is a class in the **runtime** module. Calls made to it at build time are **recorded as generated bytecode** and **replayed at startup**. `ExecutionTime.STATIC_INIT` or `RUNTIME_INIT` decides in which phase the replay happens.

**Why it matters:** the work is done once at build time, and only the cheap result runs at startup.

---

## Part 6: Messaging, Security and Deployment

### 22. Reactive messaging with Kafka

**Short answer:** Quarkus uses **SmallRye Reactive Messaging** (MicroProfile Reactive Messaging) with the **Kafka connector**. You write plain methods annotated with `@Incoming` and `@Outgoing`, and configuration maps **channels** to Kafka topics.

**Dependency:** `quarkus-messaging-kafka`. In dev and test, **Dev Services** start a Kafka broker for you.

#### Concepts
- **Channel:** a named logical stream in your code.
- **Connector:** connects a channel to an external system (here `smallrye-kafka`).
- **Message:** a payload plus metadata and ack/nack functions.

#### Consuming and producing

```java
@ApplicationScoped
public class OrderProcessor {

    @Incoming("orders-in")             // consume
    @Outgoing("orders-out")            // and publish the returned value
    public Order process(Order order) {
        order.status = "PROCESSED";
        return order;
    }
}
```

```properties
mp.messaging.incoming.orders-in.connector=smallrye-kafka
mp.messaging.incoming.orders-in.topic=orders
mp.messaging.incoming.orders-in.group.id=order-service
mp.messaging.incoming.orders-in.auto.offset.reset=earliest
mp.messaging.incoming.orders-in.failure-strategy=dead-letter-queue

mp.messaging.outgoing.orders-out.connector=smallrye-kafka
mp.messaging.outgoing.orders-out.topic=processed-orders
```

(Serializers and deserializers are often auto-detected from the types. Otherwise set `value.serializer` and `value.deserializer`, for example `ObjectMapperSerializer` and a custom `ObjectMapperDeserializer<Order>` for JSON.)

#### Producing from imperative code (for example a REST endpoint)

```java
@Path("/orders")
public class OrderResource {

    @Inject
    @Channel("orders-out")
    Emitter<Order> emitter;

    @POST
    public Response create(Order order) {
        emitter.send(order);
        return Response.accepted().build();
    }
}
```

#### Points interviewers like
- **Delivery semantics:** at-least-once by default. Make consumers **idempotent**.
- **Acknowledgement:** use `Message<T>` for manual ack/nack. Failure strategies include `fail`, `ignore`, and `dead-letter-queue`.
- **Backpressure:** reactive streams handle it, so a slow consumer doesn't get overwhelmed.
- **Blocking code:** annotate with `@Blocking` so processing runs on a worker thread instead of the event loop.
- **Streams and types:** methods can take or return `Uni`, `Multi`, or `CompletionStage`.
- **Other connectors:** AMQP, RabbitMQ, MQTT, Pulsar, and an in-memory connector for tests.
- **Related:** `quarkus-kafka-streams` for stream processing, and Apicurio/Confluent registry integration for Avro or Protobuf schemas.

---

### 23. Securing an app with OIDC

**Short answer:** Add the **`quarkus-oidc`** extension and point it at an OpenID Connect provider (Keycloak, Auth0, Okta, Azure AD, and so on). Quarkus then handles authentication, and you control authorization with annotations or configuration.

#### Two main modes (`quarkus.oidc.application-type`)

| Mode | Use case | Flow |
|---|---|---|
| **`service`** (default) | REST APIs / microservices | Client sends `Authorization: Bearer <token>`. Quarkus **verifies** the token. |
| **`web-app`** | Server-rendered apps with browser login | **Authorization Code flow**: redirects the user to the provider, then keeps a session cookie. |
| `hybrid` | Both in one app | Supports both bearer and code flow. |

#### Configuration (service mode)

```properties
quarkus.oidc.auth-server-url=https://keycloak.example.com/realms/my-realm
quarkus.oidc.client-id=my-api
quarkus.oidc.credentials.secret=${OIDC_SECRET}

# Map roles from the token (Keycloak realm roles example)
quarkus.oidc.roles.role-claim-path=realm_access/roles
```

#### Authorization

```java
@Path("/admin")
public class AdminResource {

    @Inject
    SecurityIdentity identity;

    @GET
    @RolesAllowed("admin")
    public String secret() {
        return "Hello " + identity.getPrincipal().getName();
    }

    @GET
    @Path("/me")
    @Authenticated                     // any logged-in user
    public String me() { ... }
}
```

Other options: `@PermitAll`, `@DenyAll`, and path-based rules:

```properties
quarkus.http.auth.permission.protected.paths=/api/*
quarkus.http.auth.permission.protected.policy=authenticated
```

You can also inject the **`JsonWebToken`** to read claims.

#### What Quarkus checks on a bearer token
- **Signature**, using keys fetched from the provider's JWKS endpoint (discovered from `auth-server-url`)
- **Issuer**, **expiry**, and optionally **audience**

#### Related topics
- **Dev Services for Keycloak:** a Keycloak container starts automatically in dev/test with a preconfigured realm, so you can try OIDC with zero setup.
- **Testing:** use `@TestSecurity(user = "alice", roles = "admin")` (`quarkus-test-security`) to skip real tokens, or get real tokens from the Dev Services Keycloak for integration tests.
- **Calling downstream services:** use **token propagation** (`quarkus-rest-client-oidc-token-propagation`) to forward the user's token, or `quarkus-oidc-client` to use **client credentials** for service-to-service calls.
- **Multi-tenancy:** Quarkus supports multiple OIDC tenants or providers in one app.
- **Best practices:** always use HTTPS, keep secrets out of the repo, use short-lived tokens, and validate the audience.

---

### 24. Kubernetes-native deployment

**Short answer:** Quarkus can **generate Kubernetes resources and the container image at build time** from your configuration. You don't hand-write the YAML for the common cases.

#### Generating manifests

Add the **`quarkus-kubernetes`** extension. When you run `./mvnw package`, it creates:

```
target/kubernetes/kubernetes.yml    (and kubernetes.json)
```

containing a **Deployment** and **Service** (optionally Ingress, ConfigMap, and so on).

```properties
quarkus.kubernetes.replicas=3
quarkus.kubernetes.namespace=my-namespace
quarkus.kubernetes.resources.requests.memory=64Mi
quarkus.kubernetes.resources.limits.memory=128Mi
quarkus.kubernetes.service-type=ClusterIP
quarkus.kubernetes.ingress.expose=true
quarkus.kubernetes.env.vars.MY_VAR=value
quarkus.kubernetes.env.secrets=db-secret
```

**Health probes are generated automatically** from `quarkus-smallrye-health` (liveness, readiness, and startup).

#### Building the container image

Add one of the **container-image extensions**:

| Extension | Notes |
|---|---|
| `quarkus-container-image-jib` | No Docker daemon required |
| `quarkus-container-image-docker` / `-podman` | Uses your Dockerfile |
| `quarkus-container-image-buildpack` | Cloud Native Buildpacks |
| `quarkus-container-image-openshift` | In-cluster build on OpenShift |

```properties
quarkus.container-image.group=myorg
quarkus.container-image.name=my-app
quarkus.container-image.registry=registry.example.com
quarkus.container-image.build=true
quarkus.container-image.push=true
```

The project also contains ready-made Dockerfiles in `src/main/docker/` (`Dockerfile.jvm`, `Dockerfile.native`, `Dockerfile.native-micro`).

#### One-command deploy

```bash
./mvnw clean package -Dquarkus.kubernetes.deploy=true
```

This builds the image, generates the manifests, and applies them to the cluster in your current kubeconfig context.

#### Related options
- **OpenShift:** `quarkus-openshift` generates OpenShift-specific resources (such as DeploymentConfig or Route), and supports S2I builds.
- **Knative:** `quarkus-knative` for serverless deployments, a natural fit for native images that scale to zero.
- **Helm:** the Helm extension can generate a chart.
- **Config at runtime:** mount ConfigMaps and Secrets as env vars or files, or use `quarkus-kubernetes-config` to read them through the Kubernetes API.
- **GitOps:** commit the generated YAML or chart, and deploy with Argo CD or Flux. Generated manifests are a starting point you can customize.

**Why "Kubernetes-native":** fast startup and low memory let pods scale and restart quickly, which allows higher density per node and quicker autoscaling.

---

## Part 7: Comparisons and Native Deep Dive

### 25. Quarkus vs Micronaut

**Short answer:** Both are modern JVM frameworks built to avoid Spring's heavy runtime reflection, and both support GraalVM native images. The main difference is **how** they move work out of runtime: **Micronaut** mostly via **compile-time annotation processing**, **Quarkus** via **build-time bytecode processing** (augmentation) driven by extensions.

| Aspect | Quarkus | Micronaut |
|---|---|---|
| Core idea | **Build-time augmentation** (Jandex index, build steps, bytecode generation) | **Compile-time AOT** via annotation processors |
| DI | **ArC** (Jakarta CDI Lite) | Micronaut's own DI (`jakarta.inject` compatible) |
| Standards | Jakarta EE and MicroProfile (JAX-RS, CDI, JPA, MP Config...) | Own APIs, plus some standard annotations |
| Data access | Hibernate ORM / Panache, reactive variants | Micronaut Data (repositories generated at compile time) |
| Reactive | Mutiny (`Uni` / `Multi`), Vert.x | Reactor / RxJava, Netty |
| Dev experience | Live reload, **Dev UI**, **Dev Services**, continuous testing | Fast restarts, Test Resources (containers) |
| Languages | Java, Kotlin, Scala (Java first-class) | Java, Kotlin, Groovy (all first-class) |
| Ecosystem | Large extension catalog, strong Red Hat/IBM backing | Smaller but solid catalog, strong cloud-provider modules |
| Startup / memory | Both much better than reflection-heavy frameworks | Comparable range; results vary by workload |

**How to answer in an interview:**
- Be fair. Both are good, and **benchmark numbers depend heavily on the workload**, so say you'd measure with your own application.
- Quarkus is an easier sell for teams that know **Jakarta EE / CDI / JPA / MicroProfile**, since their knowledge transfers directly.
- Micronaut can be appealing if you want compile-time everything, or first-class Groovy support.
- Practical factors often decide: team familiarity, available libraries and integrations, community and vendor support, and tooling.

> "I'd pick based on the team's existing skills, the ecosystem we need, and a quick proof-of-concept measurement. Both solve the same core problem."

---

### 26. How does native compilation reduce memory footprint?

**Short answer:** A native executable contains **only the code the app can actually reach**, is already compiled to machine code, and carries **no JVM machinery** (JIT compiler, class loader and metadata, bytecode interpreter), so far less memory is used at startup and while idle.

#### Where the savings come from

1. **Closed-world analysis (tree shaking):** GraalVM analyzes everything reachable from your entry points at build time and **drops the rest**: unused classes, methods, and library code. A smaller binary means less loaded code in memory.
2. **No JIT compiler in the process:** a normal JVM carries the JIT compilers, profiling data, and compiled-code caches. A native image is compiled ahead of time, so **none of that exists at runtime**.
3. **No class loading and parsing at startup:** the JVM must load, verify, and store metadata for thousands of classes. In native mode this is **precomputed**, so there is no class-loading memory or CPU spike.
4. **Pre-initialized heap:** a lot of initialization (Quarkus's static init, for example) runs **during the build**, and the resulting objects are stored in the image's heap snapshot. At runtime the app starts with that state already in place instead of building it.
5. **Lean runtime (Substrate VM):** a small runtime with a simpler garbage collector (Serial GC by default) replaces the full HotSpot JVM.
6. **Quarkus helps further:** build-time processing means fewer reflection and proxy structures need to be kept around.

#### Result
- Much faster startup (often milliseconds) and noticeably lower **RSS**, especially at startup and idle. Exact savings depend on the app, so measure your own service.
- Good fit for **serverless, scale-to-zero, and high-density Kubernetes** workloads where you pay for memory and cold starts.

#### Trade-offs to mention (this shows depth)
- **Longer build times** and more resource use during the native build.
- **Peak throughput** for long-running, CPU-heavy workloads can be lower than a warmed-up JIT (profile-guided optimization can narrow the gap, depending on the GraalVM distribution).
- **Closed-world limits:** reflection, dynamic proxies, resources, and serialization must be **registered at build time**. Quarkus extensions do this for you, but third-party libraries may need extra configuration (`@RegisterForReflection`, config files).
- **Tooling differences:** no JVM agents, and debugging and profiling work differently.
- **Memory under load:** the gap narrows as the heap fills. GC choice and heap settings (`-Xmx`) still matter.

> "Native mode swaps runtime flexibility for a smaller, faster-starting binary. I'd choose it for short-lived or densely packed workloads, and stay on the JVM for long-running services where JIT performance matters more."

---

## Quick-Revision Cheat Sheet

- **Quarkus** = cloud-native Java, fast startup, low memory, great DX.
- **Secret sauce** = **build-time** processing.
- **GraalVM** = only needed for **native** mode, not JVM mode.
- **Modes** = JVM and native.
- **Extensions** = runtime + deployment modules.
- **Create project** = CLI, Maven plugin, or code.quarkus.io.
- **Dev mode** = `quarkus dev`: live reload, Dev UI, Dev Services, continuous testing.
- **DI** = ArC (CDI Lite), build-time.
- **Scopes** = `@ApplicationScoped`, `@Singleton`, `@RequestScoped`, `@Dependent` (default).
- **Qualifiers** = `@Named`, `@Default`, `@Any`, or custom.
- **vs Spring DI** = build-time and standard CDI vs runtime and Spring model.
- **Panache** = Hibernate ORM simplified; Active Record (`PanacheEntity`) or Repository (`PanacheRepository`).
- **Profiles** = `%dev.`, `%test.`, `%prod.`; env vars override (`QUARKUS_DATASOURCE_JDBC_URL`).
- **REST Client** = annotated interface, implementation generated at build time (`@RegisterRestClient`, `@RestClient`).
- **OpenAPI** = SmallRye OpenAPI (`/q/openapi`, `/q/swagger-ui`); **Health** = SmallRye Health (`/q/health/live`, `/ready`, `/started`).
- **RESTEasy Reactive / Quarkus REST** = Vert.x based; `T` runs on worker thread, `Uni`/`Multi` on event loop.
- **Mutiny** = `Uni` (0..1) and `Multi` (0..N); lazy, and the framework subscribes for you.
- **Testing** = `@QuarkusTest` + RestAssured, `@InjectMock`, `@QuarkusIntegrationTest`.
- **Dev Services** = auto-started containers for dev/test only; skipped when you configure the URL yourself.
- **Build lifecycle** = compile, Jandex index, **augmentation** (build steps), output `quarkus-app/`, optional native-image. Startup replays recorded bytecode (static init, then runtime init).
- **`quarkus dev`** = augmentation in-process, two classloaders (base + reloadable), reload on change, Dev UI, Dev Services, continuous testing.
- **BuildItem** = typed data passed between `@BuildStep` methods; steps form a DAG; extension = runtime module + deployment module; **recorders** bridge build time to startup.
- **Kafka** = SmallRye Reactive Messaging: `@Incoming`, `@Outgoing`, `Emitter`, channels mapped to topics via `mp.messaging.*`; at-least-once, so make consumers idempotent.
- **OIDC** = `quarkus-oidc`; `service` (bearer token) vs `web-app` (code flow); `@RolesAllowed`, `@Authenticated`; Keycloak Dev Services; `@TestSecurity`.
- **Kubernetes** = `quarkus-kubernetes` generates manifests, container-image extensions build images, `-Dquarkus.kubernetes.deploy=true` deploys.
- **vs Micronaut** = both avoid runtime reflection; Quarkus uses build-time bytecode augmentation and CDI/Jakarta standards, Micronaut uses compile-time annotation processing and its own DI.
- **Native memory savings** = tree shaking, no JIT or class-loading metadata, pre-initialized heap, lean runtime; trade-offs are build time, closed-world limits, and peak throughput.
