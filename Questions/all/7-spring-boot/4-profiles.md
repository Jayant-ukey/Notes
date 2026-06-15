# Que - What are Profiles in Spring Boot?

### 1. Direct Answer (What)

**Spring Boot Profiles** are a way to **separate application configurations for different environments** such as:

* Development (`dev`)
* Testing (`test`)
* Production (`prod`)

👉 In simple terms:

> Profiles allow you to run the same application with different configurations based on the environment.

---

### 2. Internal Understanding (How it works)

When Spring Boot starts:

1. It checks the active profile
2. Loads profile-specific configuration files
3. Overrides default configuration if needed

Example:

```text id="pr1"
application-dev.properties
application-test.properties
application-prod.properties
```

If active profile = `dev`, Spring loads:

```text id="pr2"
application.properties + application-dev.properties
```

---

### 3. How to define and use Profiles

---

#### 🔹 1. In application.properties

```properties id="pr3"
spring.profiles.active=dev
```

---

#### 🔹 2. Using YAML

```yaml id="pr4"
spring:
  profiles:
    active: dev
```

---

#### 🔹 3. Profile-specific configuration files

```text id="pr5"
application-dev.properties
```

```properties id="pr6"
server.port=8081
db.url=dev-db-url
```

```text id="pr7"
application-prod.properties
```

```properties id="pr8"
server.port=8080
db.url=prod-db-url
```

---

### 4. Profile-based Bean Creation

You can also control beans using `@Profile`:

```java id="pr9"
@Service
@Profile("dev")
public class DevEmailService implements EmailService {
}
```

```java id="pr10"
@Service
@Profile("prod")
public class ProdEmailService implements EmailService {
}
```

👉 Only one bean will be loaded depending on the active profile.

---

### 5. Real-world Usage (Production perspective)

In real Spring Boot microservices, profiles are used for:

#### ✔ Environment separation

* Dev → local database, debug logs
* QA/Test → test database, mock services
* Prod → real DB, optimized configs

#### ✔ External integrations

* Different API endpoints per environment
* Different credentials per environment

#### ✔ Feature control

* Enable/disable features in specific environments

---

### 6. Ways to set active profile (important in interviews)

#### 🔹 1. application.properties

```properties id="pr11"
spring.profiles.active=dev
```

#### 🔹 2. JVM argument

```bash id="pr12"
-Dspring.profiles.active=prod
```

#### 🔹 3. Environment variable

```bash id="pr13"
SPRING_PROFILES_ACTIVE=prod
```

#### 🔹 4. Command line

```bash id="pr14"
java -jar app.jar --spring.profiles.active=prod
```

---

### 7. Best Practices / Production Considerations

✔ Never hardcode production values in code
✔ Use profiles for environment-specific configs
✔ Keep secrets outside code (use vault/secrets manager)
✔ Avoid too many profiles (keep it simple: dev/test/prod)
✔ Combine with `@ConfigurationProperties` for clean config management
✔ Use logging profiles for different verbosity levels

---

### 8. Key Points Interviewers Look For

* Understanding of environment-based configuration
* How Spring loads profile-specific properties
* Use of `@Profile` for beans
* Multiple ways to activate profiles
* Real-world use in microservices
* Separation of dev/test/prod environments
* Awareness of best practices in production

---

### 9. Common Follow-up Questions

1. What happens if no profile is specified?
2. Can multiple profiles be active at once?
3. Difference between @Profile and application-{env}.properties?
4. How does Spring resolve property conflicts?
5. Can beans be loaded conditionally using profiles?
6. How do profiles help in microservices deployment?
7. What is the default profile in Spring Boot?

---

### One-Line Senior-Level Summary

> "Spring Boot profiles allow us to define environment-specific configurations and beans, enabling the same application to behave differently in dev, test, and production environments by activating different configuration sets and conditional beans."
