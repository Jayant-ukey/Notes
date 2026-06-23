## Que 1 - Why Service layer? Why not call Repository directly from Controller?

The Service layer acts as the business layer between the Controller and Repository.

The Controller should only handle HTTP requests and responses.

The Repository should only interact with the database.

Business logic such as validations, calculations, transaction management, calling multiple repositories, external API calls, and exception handling should be placed in the Service layer.

If the Controller directly calls the Repository, business logic gets scattered across controllers, making the application harder to maintain, test, and reuse.

---

## Que 2- How would you implement soft delete?

Soft delete means we don't physically remove data from the database. Instead, we mark the record as deleted using a flag such as deleted = true or a timestamp like deleted_at.

This helps with auditing, data recovery, reporting, and maintaining referential integrity. A common implementation in Spring Boot/Hibernate is using a deleted column along with Hibernate annotations such as @SQLDelete and @SQLRestriction (or @SoftDelete in newer Hibernate versions).
