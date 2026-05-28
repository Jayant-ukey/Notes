Yes — if there are **2 beans of the same type** and Spring cannot determine which one to inject, it will throw an exception.

For a **5-year experienced candidate**, interviewer expects:

* Understanding of bean resolution
* Dependency injection behavior
* Ways to resolve ambiguity

---

# Example Scenario

Suppose we have:

```java id="r6z0j0"
@Component
public class PaypalService implements PaymentService {
}
```

```java id="2d8zqf"
@Component
public class StripeService implements PaymentService {
}
```

Now:

```java id="vjlwmk"
@Autowired
private PaymentService paymentService;
```

Spring finds:

* PaypalService
* StripeService

Both are type `PaymentService`.

Now Spring gets confused:

> “Which bean should I inject?”

---

# What Exception Occurs?

Spring throws:

```text id="h54jtz"
NoUniqueBeanDefinitionException
```

Example message:

```text id="bd9fzg"
expected single matching bean but found 2
```

---

# How to Resolve It?

This is the important part interviewer expects.

---

# 1. Use @Qualifier (Most Common)

```java id="yuq4ys"
@Component("paypalService")
public class PaypalService implements PaymentService {
}
```

```java id="cd0u0q"
@Autowired
@Qualifier("paypalService")
private PaymentService paymentService;
```

This explicitly tells Spring which bean to inject.

---

# 2. Use @Primary

Mark one bean as default.

```java id="7s4y6l"
@Component
@Primary
public class PaypalService implements PaymentService {
}
```

Now Spring injects this bean automatically if no qualifier is specified.

---

# 3. Inject All Beans as List or Map

Sometimes we intentionally need multiple implementations.

```java id="x6h1p5"
@Autowired
private List<PaymentService> paymentServices;
```

or

```java id="xq2tjt"
@Autowired
private Map<String, PaymentService> services;
```

Useful in:

* Strategy pattern
* Dynamic processing

---

# Internal Bean Resolution Order

Spring resolves dependency roughly in this order:

1. By Type
2. By Qualifier
3. By @Primary
4. By Bean Name

If ambiguity still exists → exception.

---

# Real Project Example

You can say:

> “In one of our projects, we had multiple notification implementations like EmailService and SmsService implementing the same interface. We used @Qualifier for explicit injection and sometimes used Map<String, NotificationService> for strategy-based dynamic selection.”

That sounds practical and experienced.

---

# Important Follow-up Question

## What if bean names match variable name?

Example:

```java id="y3j55u"
@Autowired
private PaymentService paypalService;
```

If bean name is also `paypalService`, Spring may inject by name automatically.

But relying on this is not considered best practice in large applications.

---

# Short Crisp Interview Answer

> If multiple beans of the same type are present and Spring cannot identify which one to inject, it throws NoUniqueBeanDefinitionException.
>
> We can resolve this using:
>
> * @Qualifier for explicit bean selection
> * @Primary to define a default bean
> * Injecting List or Map when multiple implementations are needed.
>
> Spring first resolves dependencies by type, then qualifier, primary annotation, and finally bean name.
