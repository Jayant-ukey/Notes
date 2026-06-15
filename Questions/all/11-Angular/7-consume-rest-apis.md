# Que - How do you consume REST APIs in Angular?

### ✅ Interview-Ready Answer (How do you consume REST APIs in Angular?)

In Angular, REST APIs are typically consumed using the **HttpClient** service provided by the `@angular/common/http` package. HttpClient allows us to perform HTTP operations such as **GET, POST, PUT, DELETE**, and it returns **Observables**, which makes handling asynchronous responses easier.

In enterprise applications, we usually create a **service layer** that is responsible for all API communication rather than calling APIs directly from components.

---

# 🔹 1. Enable HttpClientModule

First, HttpClient must be configured in the application.

```ts
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [HttpClientModule]
})
export class AppModule {}
```

---

# 🔹 2. Create a Service to Consume APIs

### User Service

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class UserService {

  private apiUrl = 'http://localhost:8080/api/users';

  constructor(private http: HttpClient) {}

  getUsers(): Observable<any> {
    return this.http.get(this.apiUrl);
  }
}
```

✔ API communication is centralized in the service layer.

---

# 🔹 3. Call Service from Component

```ts
constructor(private userService: UserService) {}

ngOnInit() {
  this.userService.getUsers().subscribe({
    next: (data) => {
      this.users = data;
    },
    error: (err) => {
      console.error(err);
    }
  });
}
```

✔ Component subscribes to the Observable and handles the response.

---

# 🔹 4. Consuming Different HTTP Methods

### GET

```ts
this.http.get('/api/users');
```

### POST

```ts
this.http.post('/api/users', userData);
```

### PUT

```ts
this.http.put('/api/users/1', userData);
```

### DELETE

```ts
this.http.delete('/api/users/1');
```

---

# 🔹 5. Error Handling

In production applications, API failures should be handled gracefully.

```ts
this.userService.getUsers().subscribe({
  next: data => this.users = data,
  error: error => {
    console.log('API Error', error);
  }
});
```

Or using RxJS:

```ts
return this.http.get(url).pipe(
  catchError(this.handleError)
);
```

---

# 🔹 6. Authentication with REST APIs

In most projects, APIs are secured using JWT tokens.

Instead of manually adding tokens everywhere, we use an **HttpInterceptor**.

```ts
intercept(req: HttpRequest<any>, next: HttpHandler) {
  const authReq = req.clone({
    setHeaders: {
      Authorization: `Bearer ${token}`
    }
  });

  return next.handle(authReq);
}
```

✔ Every outgoing API request automatically includes the token.

---

# 🔹 7. Real-World Project Usage

In a Spring Boot + Angular application:

* Angular component calls a service.
* Service uses HttpClient to call Spring Boot REST APIs.
* JWT token is added through an interceptor.
* Response is received as an Observable.
* Component subscribes and updates the UI.

Example:

* User Management Screen → GET `/users`
* Registration Form → POST `/users`
* Update Profile → PUT `/users/{id}`
* Delete User → DELETE `/users/{id}`

---

# 📌 Key Points Interviewers Look For

* Use of **HttpClient**
* API calls should be in **services**, not components
* Understanding of **Observables**
* Knowledge of GET, POST, PUT, DELETE
* Error handling approach
* Use of **HttpInterceptor** for JWT authentication
* Real-world Angular ↔ Spring Boot integration

---

# ⚠️ Common Follow-up Questions

* Why does HttpClient return Observables instead of Promises?
* What is the difference between Observables and Promises?
* What is an HttpInterceptor?
* How do you handle global API errors?
* How do you cancel an API request in Angular?
* How do you handle loading indicators while waiting for API responses?

---

# 🧾 Short Answer (40–50 seconds)

“In Angular, REST APIs are consumed using the HttpClient service from `@angular/common/http`. The common practice is to create a service layer where all API calls are implemented using methods like GET, POST, PUT, and DELETE. HttpClient returns Observables, and components subscribe to these Observables to receive data asynchronously. In production applications, we handle errors using RxJS operators and use HttpInterceptors to automatically attach JWT tokens to requests. This approach keeps the code modular, reusable, and easy to maintain when integrating Angular with Spring Boot backend services.”

