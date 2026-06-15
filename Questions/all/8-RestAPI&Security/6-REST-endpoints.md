# Que -  If you are designing a REST endpoint to update user profile details, which annotation would you use for:
   - User ID?
   - Query parameters?
   - JSON request body?

---

## ✅ How I would design the REST endpoint

### 📌 1. User ID → `@PathVariable`

Because it identifies a specific user resource in the URL.

```java
@PutMapping("/users/{id}")
public User updateUser(@PathVariable Long id, ...)
```

---

### 📌 2. Query parameters → `@RequestParam`

Used for optional metadata like source, flags, or filters (not core update data).

```java
@RequestParam(required = false) String source
```

Example:

```
PUT /users/101?source=mobile
```

---

### 📌 3. JSON request body → `@RequestBody`

Used to send the actual profile data to be updated.

```java
@RequestBody UserProfileDto userProfile
```

Example JSON:

```json
{
  "name": "John",
  "email": "john@example.com"
}
```

---

# 🧱 Final API design example

```java
@PutMapping("/users/{id}")
public User updateUser(
        @PathVariable Long id,
        @RequestParam(required = false) String source,
        @RequestBody UserProfileDto profile) {

    return service.updateUser(id, profile);
}
```

---

# 🎯 40–50 sec interview answer (concise)

For updating a user profile in a REST API, I would use `@PathVariable` for the user ID because it identifies the specific user resource in the URL. I would use `@RequestBody` to pass the user profile details in JSON format since it represents the structured data that needs to be updated. If there are any optional or metadata parameters like request source or flags, I would use `@RequestParam` in the query string. This separation ensures proper REST design where path variables identify resources, request parameters handle optional filters, and request body carries the actual update payload.
