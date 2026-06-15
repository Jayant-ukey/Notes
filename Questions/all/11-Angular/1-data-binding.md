# Que - Explain the concept of Data Binding in Angular.

### ✅ Interview-Ready Answer (Data Binding in Angular)

In Angular, **Data Binding is the mechanism that connects the component (TypeScript logic) with the template (HTML view)**. It allows automatic synchronization of data between the UI and the business logic, so we don’t need to manually manipulate the DOM like in traditional JavaScript.

Angular supports **four types of data binding**, and each has a specific use case in real applications.

---

# 🔹 1. Interpolation (One-Way Data Binding: TS → HTML)

Used to display data from component to template.

### 👉 Example:

```html
<h1>{{ title }}</h1>
```

```ts
title = 'Angular App';
```

✔ Used for displaying dynamic values in UI

---

# 🔹 2. Property Binding (One-Way Binding: TS → HTML element property)

Used to bind component data to HTML element properties.

### 👉 Example:

```html
<img [src]="imageUrl">
```

```ts
imageUrl = 'assets/logo.png';
```

✔ Used when we want to bind DOM properties dynamically

---

# 🔹 3. Event Binding (HTML → TS)

Used to handle user interactions like clicks, input changes, etc.

### 👉 Example:

```html
<button (click)="onClick()">Click Me</button>
```

```ts
onClick() {
  console.log('Button clicked');
}
```

✔ Used to send data/events from UI to component

---

# 🔹 4. Two-Way Data Binding (HTML ↔ TS)

This is a combination of property + event binding.

👉 Mostly used in forms.

### Example:

```html
<input [(ngModel)]="name">
<p>{{ name }}</p>
```

```ts
name = '';
```

✔ Any change in input updates component and vice versa

---

# 🔹 Real-World Usage in Projects

In enterprise Angular applications:

* **Interpolation** → Display API data in UI tables/cards
* **Property binding** → Dynamically enable/disable buttons, images, styles
* **Event binding** → Handle user actions (submit, click, search)
* **Two-way binding** → Forms, login pages, filters

👉 Example:
In a Spring Boot + Angular project, form data entered in Angular is sent to backend APIs using two-way binding + HTTP calls.

---

# 📌 Key Points Interviewers Look For

* Understanding of all 4 binding types
* Direction of data flow (TS ↔ HTML)
* Real use cases (forms, events, API display)
* Difference between property binding and interpolation
* Awareness that two-way binding uses `ngModel`

---

# ⚠️ Common Follow-up Questions

* Difference between interpolation and property binding?
* What is `ngModel` internally doing?
* Can we use two-way binding without forms?
* What is event binding and how does it work?
* When should we avoid two-way binding?

---

# 🧾 Short Answer (40–50 seconds)

“In Angular, data binding is the mechanism that connects the component logic with the HTML template. There are four types of data binding: interpolation, which is used to display component data in the template; property binding, which binds component data to HTML element properties; event binding, which handles user actions like clicks and sends data from the UI to the component; and two-way data binding, which combines both property and event binding using ngModel, commonly used in forms. Data binding helps in automatic synchronization between the UI and business logic, reducing manual DOM manipulation.”

---

Send your next Angular question 👍
