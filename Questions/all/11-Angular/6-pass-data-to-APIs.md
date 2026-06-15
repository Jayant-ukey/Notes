# Que - How do you pass data to REST APIs from Angular?

### ✅ Interview-Ready Answer (How do you pass data to REST APIs from Angular?)

In Angular, we typically use the **HttpClient** service from `@angular/common/http` to communicate with REST APIs. Data can be passed to APIs in different ways depending on the HTTP method and API design.

In real projects, I mostly interact with Spring Boot REST APIs using **GET, POST, PUT, and DELETE** requests.

---

# 🔹 1. Passing Data in Request Body (POST/PUT)

This is the most common approach for creating or updating data.

### Angular:

```ts
constructor(private http: HttpClient) {}

createUser(user: any) {
  return this.http.post('/api/users', user);
}
```

### Request Payload:

```json
{
  "name": "John",
  "email": "john@example.com"
}
```

### Spring Boot:

```java
@PostMapping("/users")
public UserDTO createUser(@RequestBody UserDTO userDTO) {
    return userService.createUser(userDTO);
}
```

✔ Commonly used for forms like registration, login, order creation, etc.

---

# 🔹 2. Passing Data as Path Variables

Used when identifying a specific resource.

### Angular:

```ts
getUser(id: number) {
  return this.http.get(`/api/users/${id}`);
}
```

### Spring Boot:

```java
@GetMapping("/users/{id}")
public UserDTO getUser(@PathVariable Long id) {
    return userService.getUser(id);
}
```

✔ Used for fetching user details, order details, etc.

---

# 🔹 3. Passing Data as Query Parameters

Used for filtering, searching, sorting, and pagination.

### Angular:

```ts
getUsers(status: string) {
  return this.http.get('/api/users', {
    params: { status: status }
  });
}
```

Generated URL:

```text
/api/users?status=ACTIVE
```

### Spring Boot:

```java
@GetMapping("/users")
public List<UserDTO> getUsers(@RequestParam String status) {
    return userService.getUsers(status);
}
```

✔ Very common for search and filter APIs.

---

# 🔹 4. Passing Headers

Used for authentication and custom metadata.

### Angular:

```ts
const headers = {
  Authorization: `Bearer ${token}`
};

return this.http.get('/api/users', { headers });
```

### Spring Boot:

```java
@GetMapping("/users")
public List<UserDTO> getUsers() {
    // JWT token validated by Spring Security
}
```

✔ Commonly used with JWT authentication.

---

# 🔹 5. Using Angular Services (Best Practice)

In real projects, we don't call APIs directly from components.

### Example:

```ts
@Injectable()
export class UserService {

  constructor(private http: HttpClient) {}

  getUsers() {
    return this.http.get('/api/users');
  }
}
```

Component:

```ts
this.userService.getUsers().subscribe(data => {
  this.users = data;
});
```

✔ Keeps code clean and maintainable.

---

# 🔹 Real-World Example

In a Spring Boot + Angular application:

1. User fills registration form.
2. Angular uses two-way binding (`ngModel` or Reactive Forms).
3. Form data is sent via `HttpClient.post()`.
4. Spring Boot receives it using `@RequestBody`.
5. Response is returned and displayed in the UI.

---

# 📌 Key Points Interviewers Look For

* Knowledge of Angular `HttpClient`
* Request body for POST/PUT
* Path variables and query parameters
* Passing JWT tokens in headers
* Use of Angular services for API calls
* Understanding of Angular ↔ Spring Boot communication

---

# ⚠️ Common Follow-up Questions

* Difference between query parameters and path variables?
* What is HttpInterceptor?
* How do you handle API errors in Angular?
* What is the difference between Observables and Promises?
* How do you send JWT tokens with every request?

---

# 🧾 Short Answer (40–50 seconds)

“In Angular, I use the HttpClient service to communicate with REST APIs. For POST and PUT requests, I pass data in the request body as JSON. For fetching specific resources, I use path variables, and for filtering or pagination, I use query parameters. Authentication tokens such as JWT are passed in request headers. In real projects, API calls are usually placed in Angular services rather than components to maintain clean architecture. These services interact with Spring Boot APIs and return Observables that components subscribe to for handling responses.”
