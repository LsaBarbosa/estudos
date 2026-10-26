# How does Dependency Injection work?

> Dependency Injection is a principle where an object receives its dependencies from an external component rather than creating them directly.
>
> In Spring, this is handled by the IoC container, which instantiates objects, resolves their dependencies, and manages their lifecycle.
>
> Constructor injection is the recommended approach because it makes dependencies explicit, facilitates testing, and ensures the object is created in a valid state.
>
> This mechanism reduces coupling between classes and improves the application's maintainability and testability.


---

# What is a Bean?

> A Bean is an object managed by the Spring container.
>
> Spring is responsible for creating the instance, configuring its dependencies, controlling its lifecycle, and making it available to other parts of the application.
>
> A Bean can be created automatically via annotations such as `@Component`, `@Service`, `@Repository`, and `@Controller`, or manually via methods annotated with `@Bean`.
>
> The default scope is Singleton, meaning there is typically only one instance of that Bean throughout the application's execution.


---

# What does Spring Boot do?

> Spring Boot simplifies Spring application development through auto-configuration, dependency management, and an embedded server.
>
> Instead of manually configuring dozens of components, Spring Boot uses conventions and automatically detects libraries present in the project to configure the application.
>
> It also offers features such as Actuator, external configuration, metrics integration, logging, and simplified support for microservices.
>
> The goal is to reduce repetitive configuration and allow the developer to focus on business logic.

---

# How does Auto-Configuration work?

> Auto-configuration is a Spring Boot mechanism that automatically configures various application components based on the dependencies present in the project and defined properties.
>
> For example, if the project includes Spring Web, Spring automatically configures a web server and the necessary components for REST APIs. If it detects Spring Data JPA and a configured database, it automatically creates the `DataSource`, `EntityManager`, and `TransactionManager`.
>
> These configurations use conditional annotations, such as `@ConditionalOnClass` and `@ConditionalOnMissingBean`, allowing auto-configurations to be overridden when necessary.

---

# What is the difference between @Component, @Service, and @Repository?

> All three annotations register a class as a Spring Bean. The main difference lies in the semantic responsibility of each one.
>
> `@Component` is a generic annotation for any Spring-managed component.
>
> `@Service` represents the business layer, making the class's intent clearer.
>
> `@Repository` represents the data access layer and offers an additional benefit: it automatically translates database-specific exceptions into exceptions within Spring's `DataAccessException` hierarchy.
>
> Although they technically behave similarly, using each annotation in the correct layer improves the architecture's organization and readability.

---

# What is the difference between @Controller and @RestController?

> `@Controller` is primarily used in MVC applications that return HTML pages.
>
> `@RestController`, on the other hand, is a combination of `@Controller` and `@ResponseBody`, indicating that methods return the HTTP response body directly, typically in JSON format.
>
> In applications that expose REST APIs, `@RestController` is the most commonly used option.


# How does @Transactional work?

> The `@Transactional` annotation defines that a method must be executed within a transaction.
>
> When the method starts, Spring opens a transaction. If it completes successfully, it commits the transaction. If an exception occurs that triggers a rollback, the changes are undone to preserve data consistency.
>
> Spring implements this behavior using proxies, which intercept the method call and automatically manage the transaction lifecycle.
>
> It is also possible to configure aspects such as propagation, isolation level, timeout, and which exceptions should trigger a rollback.

---

# How do you handle exceptions?

I would centralize handling using `@RestControllerAdvice` with methods annotated with `@ExceptionHandler`.
This way, exceptions are converted into standardized HTTP responses, keeping controllers focused solely on business logic.
I would also distinguish between business exceptions and unexpected exceptions. For known errors, I would return codes such as 400, 404, or 409, while internal errors would be treated as 500—logging sufficient context for investigation without exposing sensitive details to the client.
I would always aim to return a consistent error structure containing information such as the timestamp, HTTP code, message, and request path.
