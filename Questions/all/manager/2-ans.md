## Que - Why Service layer? Why not call Repository directly from Controller?

The Service layer acts as the business layer between the Controller and Repository.

The Controller should only handle HTTP requests and responses.

The Repository should only interact with the database.

Business logic such as validations, calculations, transaction management, calling multiple repositories, external API calls, and exception handling should be placed in the Service layer.

If the Controller directly calls the Repository, business logic gets scattered across controllers, making the application harder to maintain, test, and reuse.
