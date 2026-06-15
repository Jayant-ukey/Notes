# Que - How do Angular components communicate with each other?

### ✅ Interview-Ready Answer (Component Communication in Angular)

In Angular, components often need to share data because applications are built using a **component-based architecture**. Angular provides several ways for components to communicate depending on their relationship (parent-child, child-parent, or unrelated components).

In real enterprise applications, choosing the right communication pattern is important for **maintainability, performance, and scalability**.

---

# 🔹 1. Parent → Child Communication (@Input)

Used when a parent component sends data to a child component.

### 👉 Example:

```ts id="c1"
@Input() userName: string;
```

```html id="c2"
<app-child [userName]="name"></app-child>
```

✔ Used to pass data from parent dashboard to child widgets/components

---

# 🔹 2. Child → Parent Communication (@Output + EventEmitter)

Used when a child component sends data or events to the parent.

### 👉 Example:

```ts id="c3"
@Output() notify = new EventEmitter<string>();

sendMessage() {
  this.notify.emit('Hello Parent');
}
```

```html id="c4"
<app-child (notify)="handleMessage($event)"></app-child>
```

✔ Used for actions like button clicks, form submissions

---

# 🔹 3. Communication Between Unrelated Components

When components are not directly related, we use shared services.

---

## ✔ Using Service with RxJS (Most Common Approach)

### Service:

```ts id="c5"
private messageSource = new BehaviorSubject<string>('');
currentMessage = this.messageSource.asObservable();

changeMessage(msg: string) {
  this.messageSource.next(msg);
}
```

### Component A (sender):

```ts id="c6"
this.service.changeMessage('Hello');
```

### Component B (receiver):

```ts id="c7"
this.service.currentMessage.subscribe(msg => {
  console.log(msg);
});
```

✔ This is widely used in real-world Angular apps

---

# 🔹 4. Using ViewChild (Parent Access Child)

Used when parent directly accesses child component methods or properties.

### Example:

```ts id="c8"
@ViewChild(ChildComponent) child!: ChildComponent;

ngAfterViewInit() {
  this.child.someMethod();
}
```

✔ Used for tight coupling scenarios (use carefully)

---

# 🔹 5. Using State Management (Large Applications)

In enterprise-level Angular apps, we use:

* **NgRx (Redux pattern)**
* Akita (less common)
* Signals (new Angular versions)

✔ Used for complex state sharing across multiple modules/components

---

# 🔹 6. Real-World Usage in Projects

In production Angular applications:

* Dashboard → Parent component
* Widgets → Child components (@Input/@Output)
* Notifications / user session → Shared service (BehaviorSubject)
* Large apps (e-commerce, admin panels) → NgRx store

👉 Example:

* Login component updates user state → shared service/NgRx
* Navbar updates dynamically based on user login status

---

# 📌 Key Points Interviewers Look For

* @Input for parent → child communication
* @Output + EventEmitter for child → parent
* Services + RxJS for unrelated components
* ViewChild for direct access (use cautiously)
* State management (NgRx) for large apps
* Understanding when to use each approach

---

# ⚠️ Common Follow-up Questions

* Difference between @Input and @Output?
* What is BehaviorSubject and why is it used?
* When should you use NgRx?
* Why is service-based communication preferred over ViewChild?
* What are signals in Angular (new versions)?
* What problems does state management solve?

---

# 🧾 Short Answer (40–50 seconds)

“In Angular, components communicate in different ways depending on their relationship. For parent-to-child communication, we use @Input to pass data, and for child-to-parent communication, we use @Output with EventEmitter. For unrelated components, we use shared services with RxJS observables like BehaviorSubject, which is widely used in real applications. In large-scale applications, we use state management libraries like NgRx to manage global state. Additionally, ViewChild can be used for direct parent-child interaction, but it should be used carefully. Choosing the right communication method is important for building scalable Angular applications.”

---

Send your next Angular question 👍
