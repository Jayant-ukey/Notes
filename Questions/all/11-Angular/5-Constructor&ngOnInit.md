# Que -  Which gets called first: Constructor or ngOnInit()?

### ✅ Interview-Ready Answer

The **constructor gets called first**, and then Angular calls **ngOnInit()** as part of the component lifecycle.

### Execution Order:

```text
1. Constructor
2. Angular sets @Input properties
3. ngOnInit()
```

---

### Example:

```ts
export class UserComponent implements OnInit {

  constructor() {
    console.log('Constructor');
  }

  ngOnInit() {
    console.log('ngOnInit');
  }
}
```

### Output:

```text
Constructor
ngOnInit
```

---

### Why does Angular call ngOnInit after the constructor?

The constructor is responsible for:

* Creating the component instance
* Injecting dependencies

At this stage, Angular has not yet initialized all component data, especially `@Input` values.

After Angular finishes setting up the component and input bindings, it calls `ngOnInit()`, which makes it the appropriate place for:

* API calls
* Component initialization
* Loading data
* Business logic

---

### Real-World Example

Suppose a parent component passes a user ID:

```html
<app-user [userId]="101"></app-user>
```

In the child component:

```ts
@Input() userId!: number;
```

Inside the constructor, `userId` may not be available yet.

Inside `ngOnInit()`, Angular has already initialized the input, so it is safe to use:

```ts
ngOnInit() {
   this.loadUser(this.userId);
}
```

---

### 📌 Key Point Interviewers Look For

A strong answer is:

> "The constructor is called first when Angular creates the component instance and injects dependencies. After Angular initializes input properties and completes component setup, it invokes ngOnInit(). That's why dependency injection is typically done in the constructor, while initialization logic and API calls are placed in ngOnInit()."

---

### ⚠️ Common Follow-up Questions

* Why should API calls be placed in ngOnInit instead of the constructor?
* Are @Input values available in the constructor?
* What lifecycle hook is called after ngOnInit?
* What is the difference between ngOnInit and ngAfterViewInit?

---

### 🧾 Short Answer (20–30 seconds)

"Constructor is called first, followed by ngOnInit. The constructor is used for dependency injection and creating the component instance, while ngOnInit is called after Angular initializes the component and its input properties. That's why API calls and initialization logic are usually placed in ngOnInit rather than the constructor."
