For a **5-year experienced Spring Boot candidate**, interviewers expect:

* Authentication vs Authorization understanding
* Spring Security flow
* JWT-based security
* Filter chain knowledge
* Real project implementation

A strong answer should explain both:

* Basic implementation
* Enterprise/JWT implementation

---

# Short Crisp Interview Answer

> Spring Security is used to implement authentication and authorization in Spring Boot applications.
>
> We configure security using SecurityFilterChain, define endpoint access rules, and use UserDetailsService for loading users. Passwords are encrypted using BCryptPasswordEncoder.
>
> In modern microservices, JWT-based authentication is commonly used. After login, the server generates a JWT token, and every request carries the token in the Authorization header. A custom JWT filter validates the token and sets authentication in the SecurityContext.
>
> Spring Security internally works using a filter chain mechanism to secure requests.

> Then read upto 2nd point which is security configure
---

# What is Spring Security?

Spring Security is a security framework used to secure Spring applications.

It provides:

* Authentication
* Authorization
* Password encryption
* Session management
* CSRF protection
* JWT/OAuth2 support

---

# Authentication vs Authorization

This is usually asked first.

| Term           | Meaning                            |
| -------------- | ---------------------------------- |
| Authentication | Verifying who the user is          |
| Authorization  | Verifying what the user can access |

Example:

* Login → Authentication
* Admin access → Authorization

---

# How to Implement Spring Security

---

# 1. Add Dependency

```xml id="9x97z9"
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

After adding dependency:

* All endpoints become secured by default.

---

# 2. Configure Security

Modern Spring Security uses `SecurityFilterChain`.

```java id="jl2x5w"
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http)
            throws Exception {

        http
            .csrf(csrf -> csrf.disable())
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .formLogin(Customizer.withDefaults());

        return http.build();
    }
}
```

---

# What Happens Internally?

Spring Security works using:

* Filter chain
* AuthenticationManager
* UserDetailsService
* SecurityContext

Request flow:

```text id="nyjsjw"
Request
  ↓
Security Filters
  ↓
Authentication
  ↓
Authorization
  ↓
Controller
```

---

# 3. In-Memory Authentication (Basic Example)

```java id="tpx2lx"
@Bean
public UserDetailsService userDetailsService() {

    UserDetails user =
        User.withUsername("admin")
            .password(passwordEncoder().encode("admin123"))
            .roles("ADMIN")
            .build();

    return new InMemoryUserDetailsManager(user);
}
```

---

# 4. Password Encryption

Never store plain passwords.

Use:

* BCryptPasswordEncoder

```java id="7jtrqw"
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

---

# 5. Database Authentication (Most Important for Real Projects)

Usually users are stored in DB.

We implement:

* User entity
* UserRepository
* UserDetailsService

---

## UserDetailsService Example

```java id="cc6a22"
@Service
public class CustomUserDetailsService
        implements UserDetailsService {

    @Autowired
    private UserRepository repository;

    @Override
    public UserDetails loadUserByUsername(String username)
            throws UsernameNotFoundException {

        User user = repository.findByUsername(username);

        return new org.springframework.security.core.userdetails.User(
                user.getUsername(),
                user.getPassword(),
                new ArrayList<>()
        );
    }
}
```

---

# 6. JWT Authentication (Most Asked in Microservices)

In modern microservices:

* Session-based auth is less common
* JWT is preferred

---

# JWT Flow

```text id="s98pjl"
Client → Login API
        ↓
Server validates credentials
        ↓
JWT Token generated
        ↓
Client sends token in Authorization header
        ↓
Spring Security validates token
```

---

# Authorization Header

```text id="4lth8y"
Authorization: Bearer eyJhbGciOi...
```

---

# JWT Advantages

* Stateless
* Scalable
* Suitable for microservices
* No server session storage

---

# JWT Components

A JWT contains:

* Header
* Payload
* Signature

---

# 7. JWT Filter

We create a custom filter extending:

```java id="4q70oi"
OncePerRequestFilter
```

This filter:

* Extracts token
* Validates token
* Sets authentication in SecurityContext

---

# Example JWT Filter Flow

```text id="b4s5p5"
Request
 ↓
JWT Filter
 ↓
Validate Token
 ↓
Set Authentication
 ↓
Controller
```

---

# 8. Role-Based Authorization

Example:

```java id="6fjg5v"
@PreAuthorize("hasRole('ADMIN')")
```

or

```java id="p7smcl"
.requestMatchers("/admin/**").hasRole("ADMIN")
```

---

# 9. OAuth2 / SSO (Senior-Level Topic)

For enterprise systems:

* Google Login
* Okta
* Keycloak

We use:

* OAuth2
* OpenID Connect

Common provider:

* Keycloak

---

# Security Best Practices (Important)

## Use HTTPS

Never expose tokens over HTTP.

---

## Encrypt Passwords

Use BCrypt.

---

## Use Stateless JWT in Microservices

Avoid sessions.

---

## Token Expiration

Always set expiry.

---

## Refresh Tokens

Used for re-authentication.

---

## Principle of Least Privilege

Give minimum required access.

---

# Real Project-Level Answer

You can say:

> “In our Spring Boot microservices, we implemented JWT-based authentication using Spring Security. Login APIs generated JWT tokens after validating credentials from PostgreSQL. A custom OncePerRequestFilter validated tokens for every request and set authentication in the SecurityContext. Role-based authorization was implemented using hasRole() and @PreAuthorize annotations.”

This sounds production-level.

---

# Common Follow-up Questions

## Why JWT over session?

| JWT                      | Session                |
| ------------------------ | ---------------------- |
| Stateless                | Stateful               |
| Better for microservices | Better for monoliths   |
| Scalable                 | Server memory required |

---

## Why OncePerRequestFilter?

Ensures filter executes once per request.

---

## What is SecurityContext?

Stores authenticated user details for current request.

---

## What is CSRF?

Cross-Site Request Forgery attack.

Usually disabled for stateless REST APIs using JWT.

---
