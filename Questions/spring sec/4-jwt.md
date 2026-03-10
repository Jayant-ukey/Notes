**Question**- How would you implement a JWT filter in Spring Security?


# 1. Concept (Explain First in Interview)

A **JWT filter** is used to:

1. Intercept every HTTP request.
2. Extract the JWT from the **Authorization header**.
3. Validate the token.
4. If valid, set authentication in the **SecurityContext**.
5. Allow the request to proceed.

In Spring Security, we implement this by extending:

```
OncePerRequestFilter
```

This ensures the filter runs **once per request**.

---

# 2. JWT Filter Implementation

Example:

```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter {

    @Autowired
    private JwtService jwtService;

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
                                    throws ServletException, IOException {

        String authHeader = request.getHeader("Authorization");
        String token = null;
        String username = null;

        if(authHeader != null && authHeader.startsWith("Bearer ")) {
            token = authHeader.substring(7);
            username = jwtService.extractUsername(token);
        }

        if(username != null && SecurityContextHolder.getContext().getAuthentication() == null){

            UserDetails userDetails = userDetailsService.loadUserByUsername(username);

            if(jwtService.validateToken(token, userDetails)){

                UsernamePasswordAuthenticationToken authToken =
                        new UsernamePasswordAuthenticationToken(
                                userDetails,
                                null,
                                userDetails.getAuthorities()
                        );

                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

---

# 3. Register Filter in Security Configuration

The filter must be added to the **security filter chain**.

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    http
        .csrf().disable()
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/auth/**").permitAll()
            .anyRequest().authenticated()
        )
        .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);

    return http.build();
}
```

This ensures the JWT filter runs **before Spring’s default authentication filter**.

---

# 4. Request Flow (Explain This Clearly)

Flow of a request:

1️⃣ User logs in with username/password  
2️⃣ Server generates JWT  
3️⃣ Client stores token  
4️⃣ Client sends request:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

5️⃣ JWT filter runs
- extracts token
- validates token
- loads user
- sets authentication

6️⃣ Spring Security allows access if authorized.

---

# 5. What Interviewers Like to Hear

If you say these keywords, they know you **actually implemented it**:

- `OncePerRequestFilter`
- `Authorization: Bearer token`
- `SecurityContextHolder`
- `UsernamePasswordAuthenticationToken`
- `addFilterBefore()`

---

# 6. Short 20-Second Interview Answer

You could say:

> To implement a JWT filter in Spring Security, I create a custom filter by extending OncePerRequestFilter. The filter extracts the JWT from the Authorization header, validates it using a JWT service, loads the user using UserDetailsService, and sets authentication in the SecurityContextHolder. Then I register this filter in the SecurityFilterChain using addFilterBefore so it runs before the UsernamePasswordAuthenticationFilter.
