
**Question:**

> Implement a REST API in Spring Boot where:
>
> * Users can register and login.
> * Passwords must be encrypted using BCrypt.
> * Only users with role `ADMIN` can access `/admin`.
> * Normal users can access `/user`.
> * Authentication should use **JSON Web Token**.

---

# What the interviewer expects

They want to see if you understand:

1. Spring Security configuration
2. JWT authentication
3. Role-based authorization
4. Password encryption

---

# Step-by-Step Solution (Simplified)

## 1. User Entity

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String username;
    private String password;
    private String role;
}
```

---

# 2. Password Encryption

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

When registering:

```java
user.setPassword(passwordEncoder.encode(user.getPassword()));
```

---

# 3. Custom UserDetailsService

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    UserRepository repo;

    @Override
    public UserDetails loadUserByUsername(String username)
            throws UsernameNotFoundException {

        User user = repo.findByUsername(username);

        return new org.springframework.security.core.userdetails.User(
                user.getUsername(),
                user.getPassword(),
                List.of(new SimpleGrantedAuthority(user.getRole()))
        );
    }
}
```

---

# 4. Security Configuration

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    http.csrf().disable()
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .requestMatchers("/user/**").hasRole("USER")
            .requestMatchers("/auth/**").permitAll()
            .anyRequest().authenticated()
        );

    return http.build();
}
```

---

# 5. Example Controllers

### Public API

```java
@RestController
@RequestMapping("/auth")
public class AuthController {

    @PostMapping("/login")
    public String login() {
        return "JWT Token Generated";
    }
}
```

---

### User API

```java
@GetMapping("/user/dashboard")
public String userDashboard() {
    return "User Dashboard";
}
```

---

### Admin API

```java
@GetMapping("/admin/dashboard")
public String adminDashboard() {
    return "Admin Dashboard";
}
```

---

# Expected API Behavior

| Endpoint      | Access     |
| ------------- | ---------- |
| `/auth/login` | Public     |
| `/user/**`    | USER role  |
| `/admin/**`   | ADMIN role |

---

# How a Strong Candidate Explains It

You could say:

> I configure Spring Security using SecurityFilterChain. Users are stored in a database and loaded via UserDetailsService. Passwords are encrypted using BCrypt. During login, credentials are verified and a JWT token is generated. The token is sent in the Authorization header for subsequent requests. Role-based authorization is configured using requestMatchers or annotations like @PreAuthorize.
