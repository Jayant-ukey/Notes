# Que - How do you handle Routing in Angular?

### ✅ Interview-Ready Answer (Routing in Angular)

In Angular, **Routing is used to navigate between different views or components in a Single Page Application (SPA)** without reloading the entire page. It is handled using the **Angular Router module (`@angular/router`)**, which maps URLs to components.

In real projects, routing is very important for building structured applications like dashboards, admin panels, and enterprise systems.

---

# 🔹 1. Setting Up Routing in Angular

First, we define routes in a routing module (commonly `app-routing.module.ts`).

### 👉 Example:

```ts id="r1"
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { LoginComponent } from './login/login.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'login', component: LoginComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

✔ This defines URL → Component mapping.

---

# 🔹 2. Router Outlet (Where Views Render)

Angular uses `<router-outlet>` as a placeholder to load components dynamically.

### 👉 Example:

```html id="r2"
<router-outlet></router-outlet>
```

✔ Acts like a container for routed components.

---

# 🔹 3. Navigation Between Routes

We can navigate in two ways:

---

## ✔ 1. Using RouterLink (Template Navigation)

```html id="r3"
<a routerLink="/login">Login</a>
```

✔ Preferred for UI navigation

---

## ✔ 2. Programmatic Navigation (TypeScript)

```ts id="r4"
import { Router } from '@angular/router';

constructor(private router: Router) {}

goToLogin() {
  this.router.navigate(['/login']);
}
```

✔ Used after business logic (e.g., login success)

---

# 🔹 4. Route Parameters (Dynamic Routing)

Used when we need to pass data in URL.

### 👉 Example:

```ts id="r5"
{ path: 'user/:id', component: UserComponent }
```

### Access parameter:

```ts id="r6"
this.route.snapshot.paramMap.get('id');
```

✔ Used in real apps like:

* User details page
* Order details page

---

# 🔹 5. Child Routes (Nested Routing)

Used in dashboards and complex layouts.

### 👉 Example:

```ts id="r7"
{
  path: 'admin',
  component: AdminComponent,
  children: [
    { path: 'users', component: UsersComponent },
    { path: 'settings', component: SettingsComponent }
  ]
}
```

✔ Helps in modular UI structure

---

# 🔹 6. Route Guards (Security in Routing)

Used to protect routes.

### Types:

* `CanActivate` → restrict access
* `CanDeactivate` → prevent leaving page
* `CanLoad` → lazy module protection

### Example:

```ts id="r8"
{ path: 'dashboard', component: DashboardComponent, canActivate: [AuthGuard] }
```

✔ Commonly used for authentication

---

# 🔹 7. Lazy Loading (Performance Optimization)

Instead of loading all modules at once:

👉 Load modules only when needed

```ts id="r9"
{
  path: 'admin',
  loadChildren: () =>
    import('./admin/admin.module').then(m => m.AdminModule)
}
```

✔ Improves initial load time significantly

---

# 🔹 8. Real-World Usage in Projects

In enterprise Angular applications:

* `/login` → Login page
* `/dashboard` → Main dashboard
* `/orders/:id` → Order details
* `/admin/*` → Lazy-loaded admin module

✔ Combined with Spring Boot backend APIs for full-stack apps

---

# 📌 Key Points Interviewers Look For

* Angular Router is used for SPA navigation
* RouterOutlet is mandatory for rendering views
* Difference between routerLink and navigate()
* Route parameters for dynamic routing
* Route guards for security
* Lazy loading for performance optimization
* Child routes for modular structure

---

# ⚠️ Common Follow-up Questions

* What is the difference between routerLink and navigate()?
* What are route guards and types?
* What is lazy loading and why is it important?
* How do you pass data between routes?
* What is the difference between SPA and MPA routing?

---

# 🧾 Short Answer (40–50 seconds)

“In Angular, routing is used to navigate between different views in a single page application without reloading the page. It is handled using the Angular Router module, where we define routes that map URLs to components. We use router-outlet as a placeholder where components are rendered dynamically. Navigation can be done using routerLink in templates or programmatically using the Router service. Angular also supports route parameters for dynamic pages, route guards for security, and lazy loading for performance optimization. In real applications, routing is used to structure pages like login, dashboard, and detail views in a modular way.”

---

Send your next Angular question 👍
