**Question:-** How do you implement authentication and authorization in spring boot?

### 1. Authentication vs Authorization

First, clarify the difference:

* **Authentication** → Verifying *who the user is* (username/password, token, etc.)
* **Authorization** → Verifying *what the user is allowed to access* (roles/permissions)

In Spring Boot, both are typically implemented using **Spring Security**.

---

# Implementation in Spring Boot

## 1. Add Spring Security Dependency

Using **Spring Boot**, we include the security starter:

```xml
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

---

# 2. Authentication Implementation

There are multiple ways to implement authentication:

### A. In-Memory Authentication (for testing)

```java
@Bean
public UserDetailsService userDetailsService() {
    UserDetails user = User.withDefaultPasswordEncoder()
        .username("admin")
        .password("password")
        .roles("ADMIN")
        .build();

    return new InMemoryUserDetailsManager(user);
}
```

This stores users in memory.

---

### B. Database Authentication (Most Common)

1. Store users in DB
2. Implement **UserDetailsService**

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username)
        throws UsernameNotFoundException {

        User user = userRepository.findByUsername(username);

        return new org.springframework.security.core.userdetails.User(
                user.getUsername(),
                user.getPassword(),
                user.getAuthorities()
        );
    }
}
```

Passwords are usually encrypted using **BCrypt.

---

# 3. Authorization Implementation

Authorization is handled using **roles or permissions**.

Example configuration:

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .requestMatchers("/user/**").hasRole("USER")
            .anyRequest().authenticated()
        )
        .formLogin();

    return http.build();
}
```

---

# 4. Role-Based Access Control

Example using annotations:

```java
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/admin/dashboard")
public String adminDashboard() {
    return "Admin Access";
}
```

---

# 5. Token-Based Authentication (JWT – Common in Microservices)

Instead of sessions, APIs often use **JSON Web Token**.

Flow:

1. User logs in with username/password
2. Server generates JWT
3. Client sends JWT in header:

```
Authorization: Bearer <token>
```

4. Spring Security filter validates the token.

---

# 6. OAuth2 / External Authentication (Optional)

For login using Google, GitHub, etc., Spring Security supports **OAuth 2.0**.



If you want, I can also give you:

* **A perfect 60-second interview answer**
* **Spring Boot + JWT authentication flow diagram**
* **Common follow-up questions interviewers ask after this** (very helpful).
