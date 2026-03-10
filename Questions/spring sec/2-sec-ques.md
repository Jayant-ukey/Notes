# 1. What is the difference between Authentication and Authorization?

**What interviewer checks:** Basics

**Good answer:**

* **Authentication** → Verifying the identity of the user (username/password, token).
* **Authorization** → Checking what resources the user is allowed to access (roles/permissions).

Example:
User logs in → Authentication
User accesses `/admin` → Authorization

---

# 2. What is UserDetailsService?

**What interviewer checks:** Spring Security core concept.

**Answer:**

`UserDetailsService` is an interface used by Spring Security to **load user-specific data from the database** during authentication.

Main method:

```java
UserDetails loadUserByUsername(String username)
```

It returns a `UserDetails` object containing:

* username
* password
* roles/authorities

---

# 3. Why do we use BCrypt for passwords?

Interviewers want to see if you understand **password security**.

Mention **BCrypt**.

**Answer:**

* BCrypt hashes passwords before storing them.
* It adds **salt** automatically.
* It protects against **rainbow table attacks**.
* It is **adaptive** (cost factor increases difficulty).

Example:

```java
PasswordEncoder encoder = new BCryptPasswordEncoder();
```

---

# 4. What is JWT and why is it used?

Mention **JSON Web Token**.

**Answer:**

JWT is a token-based authentication mechanism used mostly in REST APIs.

Flow:

1. User logs in
2. Server validates credentials
3. Server generates JWT
4. Client sends JWT in request header

```
Authorization: Bearer token
```

Benefits:

* Stateless
* No session storage
* Good for microservices

---

# 5. What is the SecurityFilterChain?

**Answer:**

In Spring Security, `SecurityFilterChain` defines **security rules for HTTP requests**.

Example:

```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http.authorizeHttpRequests(auth -> auth
        .requestMatchers("/admin/**").hasRole("ADMIN")
        .anyRequest().authenticated()
    );
    return http.build();
}
```

It controls:

* authentication
* authorization
* login/logout
* CSRF protection

---

# 6. What is the difference between hasRole() and hasAuthority()?

**Answer:**

* `hasRole("ADMIN")`

  * Automatically adds prefix `ROLE_`
  * Checks `ROLE_ADMIN`

* `hasAuthority("ADMIN")`

  * Checks authority exactly as defined

Example:

```java
.hasRole("ADMIN")
.hasAuthority("ADMIN")
```

---

# 7. What is CSRF in Spring Security?

Mention **Cross-Site Request Forgery**.

**Answer:**

CSRF is an attack where a malicious site performs actions on behalf of an authenticated user.

Spring Security protects against this using **CSRF tokens**.

For stateless APIs using JWT, we usually disable it:

```java
http.csrf().disable();
```

---

# 8. How do you secure REST APIs?

Expected points:

* JWT authentication
* HTTPS
* Role-based authorization
* Input validation
* Rate limiting

---

# 9. How do you implement method-level security?

Using annotations:

```java
@PreAuthorize("hasRole('ADMIN')")
```

Enable it:

```java
@EnableMethodSecurity
```

---

# 10. What is OAuth2?

Mention **OAuth 2.0**.

Used for **third-party authentication** like:

* Google login
* GitHub login
* Facebook login

Spring Security has built-in OAuth2 support.
