# Gurutattva API – Interview Questions

## 1. Clean / Onion Architecture & Layer Separation

### ⬜ Beginner

**Q1.** What is the primary purpose of the `GurutattvaAPI.Domain` project, and why shouldn't it have any project references?
> 💡 *What the interviewer is looking for: Understanding that the domain is the core, containing pure business logic and models, and must remain independent of external frameworks or layers to ensure testability and portability.*

**Q2.** Which layer in this solution is responsible for mapping incoming HTTP requests to business logic, and where are the controllers located?
> 💡 *What the interviewer is looking for: Identifying the Presentation/Host layer (`GurutattvaAPI`) as the entry point and the place where API-specific concerns like controllers reside.*

**Q3.** If you need to add a new third-party email service, in which project would the concrete implementation belong?
> 💡 *What the interviewer is looking for: The Infrastructure layer (`GurutattvaAPI.Infrastructure`), which handles external concerns and implementations of interfaces.*

### 🟨 Intermediate

**Q4.** How does the `GurutattvaAPI.Application` layer interact with the `Infrastructure` layer without having a direct dependency on its concrete implementations?
> 💡 *What the interviewer is looking for: Knowledge of the Dependency Inversion Principle; the Application layer defines interfaces, and Infrastructure implements them.*

**Q5.** Why do we separate `GurutattvaAPI.Contracts` into its own project instead of just putting DTOs in the Application layer?
> 💡 *What the interviewer is looking for: Understanding that Contracts are the public interface of the API, shared with external consumers or the Presentation layer, and keeping them separate prevents leaking internal Application models.*

### 🟥 Advanced

**Q6.** In a Clean Architecture setup like Gurutattva, how do you handle cross-cutting concerns (like logging or validation) without cluttering the Domain or Application logic?
> 💡 *What the interviewer is looking for: Use of Middleware, Action Filters, or MediatR Behaviors to inject logic into the request pipeline or use case execution without modifying the core logic.*

### 🎯 Scenario-Based

**Q7.** You are asked to replace the SQL database with a NoSql alternative. Walk me through the changes required across the solution. Which projects remain untouched?
> 💡 *What the interviewer is looking for: The candidate should realize that Domain and Application projects remain untouched. Changes are isolated to `GurutattvaAPI.Infrastructure` and DI registration in the Host layer.*

### 💬 Behavioral

**Q8.** Tell me about a time you had to push back on adding a dependency to the Domain layer. What was the dependency, and how did you resolve the requirement instead?
> 💡 *What the interviewer is looking for: Commitment to architectural integrity and ability to find Clean Architecture-compliant workarounds.*

---

## 2. Dependency Injection & Service Registration

### ⬜ Beginner

**Q1.** Where does the main dependency injection registration happen in the `GurutattvaAPI` solution?
> 💡 *What the interviewer is looking for: Identifying `Program.cs` in the Host layer as the central place for DI configuration.*

**Q2.** What is the purpose of the `InfrastructureConfiguration.cs` file in the `GurutattvaAPI.Infrastructure` project?
> 💡 *What the interviewer is looking for: Understanding the "Service Collection Extension" pattern used to keep `Program.cs` clean by delegating layer-specific registrations to the layer itself.*

### 🟨 Intermediate

**Q3.** If you register a service as "Scoped" in this API, how long will that instance live during an incoming web request?
> 💡 *What the interviewer is looking for: Correctly identifying that a Scoped instance lives for the duration of a single HTTP request.*

**Q4.** When would you choose to use `AddTransient` over `AddScoped` for a service implementation in the `Application` layer?
> 💡 *What the interviewer is looking for: Explaining that Transient is for lightweight, stateless services where a new instance is needed every time it's requested.*

### 🟥 Advanced

**Q5.** How would you handle a circular dependency between two services in the `GurutattvaAPI.Application` layer while maintaining layer separation?
> 💡 *What the interviewer is looking for: Suggesting refactoring to a common service, using events/MediatR, or leveraging lazy initialization (though refactoring is preferred).*

### 🎯 Scenario-Based

**Q6.** You notice that a specific Repository in `GurutattvaAPI.Infrastructure` is using a significant amount of memory. Upon investigation, you find it's registered as a Singleton but holds onto a `DbContext`. What is the danger here, and how do you fix it?
> 💡 *What the interviewer is looking for: Capturing a Scoped context in a Singleton leads to memory leaks and threading issues. The fix is to change the scope or use a `IDbContextFactory`.*

### 💬 Behavioral

**Q7.** Describe a scenario where you had to decide between manual instantiation and DI. Why did you choose DI for this project?
> 💡 *What the interviewer is looking for: Appreciation for testability, modularity, and managing object lifetimes.*

---

## 3. Entity Framework Core

### ⬜ Beginner

**Q1.** How do you apply new database changes to the SQL database in this project?
> 💡 *What the interviewer is looking for: Mentioning EF Core Migrations and the `Update-Database` command (or the auto-migration logic on startup).*

**Q2.** What is the role of the `DbContext` class found in the `Infrastructure` project?
> 💡 *What the interviewer is looking for: Acts as a bridge between the Domain entities and the physical database.*

### 🟨 Intermediate

**Q3.** Explain the difference between `IEnumerable` and `IQueryable` when writing a repository method in `GurutattvaAPI.Infrastructure`.
> 💡 *What the interviewer is looking for: `IQueryable` allows for server-side filtering (SQL), while `IEnumerable` performs filtering in-memory after data is fetched.*

**Q4.** Why might you use `AsNoTracking()` for certain read-only queries in this API?
> 💡 *What the interviewer is looking for: Performance optimization; EF Core doesn't need to track changes for objects that won't be updated, reducing overhead.*

### 🟥 Advanced

**Q5.** How would you implement a "Soft Delete" mechanism globally for all entities in `GurutattvaAPI` using EF Core Global Query Filters?
> 💡 *What the interviewer is looking for: Overriding `OnModelCreating` to apply a filter like `HasQueryFilter(e => !e.IsDeleted)` and intercepting `SaveChangesAsync` to set the flag.*

### 🎯 Scenario-Based

**Q6.** The `MigrationSettings.EnableAutoMigration` is true, and the API is starting up in a production environment with multiple instances. How do you prevent race conditions or schema corruption when multiple pods try to run migrations simultaneously?
> 💡 *What the interviewer is looking for: Discussing distributed locks, or better yet, recommending running migrations as a separate step in the CI/CD pipeline rather than on application start.*

### 💬 Behavioral

**Q7.** Have you ever encountered a "Migration Hell" scenario? How did you resolve conflicting migrations in a team environment?
> 💡 *What the interviewer is looking for: Experience with Git, rebasing migrations, and communication within the team.*

---

## 4. Domain Modeling

### ⬜ Beginner

**Q1.** What is the difference between an `Entity` and an `Enum` as seen in the `GurutattvaAPI.Domain` project?
> 💡 *What the interviewer is looking for: Entities have identity and state; Enums represent a fixed set of named constants.*

**Q2.** Why would you use a `ValueObject` (like a `Money` or `Address` type) instead of just primitive types in the Domain layer?
> 💡 *What the interviewer is looking for: Encapsulation of validation logic and ensuring that the object represents a valid concept without needing a separate ID.*

### 🟨 Intermediate

**Q3.** When should logic be placed in a `Domain Service` instead of inside an `Entity`?
> 💡 *What the interviewer is looking for: When the logic involves multiple entities or external lookups that don't belong to the internal state of a single entity.*

**Q4.** How do you enforce business rules within a Domain Entity to ensure it's never in an invalid state?
> 💡 *What the interviewer is looking for: Using private setters and specialized methods for state transitions, rather than public properties.*

### 🟥 Advanced

**Q5.** Discuss the pros and cons of using Rich Domain Models vs. Anemic Domain Models in a project like Gurutattva.
> 💡 *What the interviewer is looking for: Rich models keep logic where the data is (OOP), while anemic models separate data and logic (common but less pure).*

### 🎯 Scenario-Based

**Q6.** You are designing the `AudioMedia` entity. It has a complex status workflow (Pending -> Processing -> Ready -> Failed). How do you model this to prevent an audio file from moving directly from Pending to Ready?
> 💡 *What the interviewer is looking for: Implementing a state machine or guard clauses within the transition methods.*

### 💬 Behavioral

**Q7.** Give an example of a business rule that was difficult to model in the Domain. How did you simplify it?
> 💡 *What the interviewer is looking for: Problem-solving skills and ability to translate requirements into code.*

---

## 5. Application Layer

### ⬜ Beginner

**Q1.** What is the primary role of a "Use Case" or "Handler" in the `GurutattvaAPI.Application` layer?
> 💡 *What the interviewer is looking for: Orchestrating the flow of data between the outside world (Request), the Domain (Logic), and Infrastructure (Persistence).*

**Q2.** Where are the application-level `Interfaces` (like `IEmailService`) defined, and why?
> 💡 *What the interviewer is looking for: Defined in Application or Domain to allow the business logic to depend on abstractions rather than concrete implementations.*

### 🟨 Intermediate

**Q3.** How does the Application layer ensure that a request is valid before it reaches the Repository?
> 💡 *What the interviewer is looking for: Mentioning the use of `Validators` (FluentValidation) triggered before or at the start of the use case execution.*

**Q4.** Explain the benefit of using the `Contracts` project for request/response objects instead of passing Domain Entities directly to the Controllers.
> 💡 *What the interviewer is looking for: Decoupling the API's external contract from the internal data model, allowing internal changes without breaking consumers.*

### 🟥 Advanced

**Q5.** If a use case involves multiple steps (e.g., Save User, Upload Image, Send Welcome Email), how do you ensure atomicity? What happens if the email fails?
> 💡 *What the interviewer is looking for: Discussion of Database Transactions (Unit of Work) and potentially an "Outbox Pattern" for external services like email.*

### 🎯 Scenario-Based

**Q6.** You have a long-running process in the `AudioProcessor` Lambda. How should the Application layer handle the initial request from the API to start this process? Should it wait?
> 💡 *What the interviewer is looking for: Recommending an asynchronous pattern: accept the request, queue a message/job, and return a `202 Accepted` status.*

### 💬 Behavioral

**Q7.** Describe a time you refactored a "Fat Controller" into the Application layer. What were the immediate benefits?
> 💡 *What the interviewer is looking for: Understanding of SOLID principles and improved testability.*

---

## 6. FluentValidation & input validation strategy

### ⬜ Beginner

**Q1.** Where do you define the validation rules for a request in `GurutattvaAPI.Application`?
> 💡 *What the interviewer is looking for: Identifying the use of `AbstractValidator<T>` classes from FluentValidation within the Application project.*

**Q2.** What is the difference between "Input Validation" and "Business Rule Validation"?
> 💡 *What the interviewer is looking for: Input validation (format/required fields) should happen early (API/Application), while business rules (domain state) happen later.*

### 🟨 Intermediate

**Q3.** How do you inject a `Validator` into the request pipeline so it runs automatically before the Use Case handler?
> 💡 *What the interviewer is looking for: Mentioning MediatR Pipeline Behaviors or custom Middleware to intercept the request and validate it.*

**Q4.** Can you use asynchronous rules in FluentValidation (e.g., checking if a username exists in the DB)? What are the performance considerations?
> 💡 *What the interviewer is looking for: Using `MustAsync` and realizing it can slow down requests if not handled properly.*

### 🟥 Advanced

**Q5.** How would you handle localized validation messages in `GurutattvaAPI`?
> 💡 *What the interviewer is looking for: Configuring the FluentValidation factory to use `IStringLocalizer` or custom message providers.*

### 🎯 Scenario-Based

**Q6.** A user submits a complex object with nested collections. One particular rule depends on the values across three different sub-items. How do you model this in a single `Validator`?
> 💡 *What the interviewer is looking for: Using `RuleForEach` combined with `When` or custom `RuleSet` logic.*

### 💬 Behavioral

**Q7.** When have you decided to duplicate a validation rule in both the Frontend and the Backend? Why?
> 💡 *What the interviewer is looking for: Balancing UX (immediate feedback) with Security (never trust the client).*

---

## 7. Contracts layer (DTOs)

### ⬜ Beginner

**Q1.** Why do we use separate Request and Response objects (DTOs) instead of the same object for both?
> 💡 *What the interviewer is looking for: Preventing over-posting/under-posting and keeping the API contract clean and minimal.*

**Q2.** Where should the conversion/mapping logic between a Contract (DTO) and a Domain Entity live?
> 💡 *What the interviewer is looking for: Typically in a Mapping profile (like AutoMapper) or a static factory/mapping method in the Application layer.*

### 🟨 Intermediate

**Q3.** What are the advantages of using `Records` (introduced in C# 9) for DTOs in the `GurutattvaAPI.Contracts` project?
> 💡 *What the interviewer is looking for: Immutability by default, value equality, and concise syntax.*

**Q4.** How do you handle versioning changes in your DTOs in a production environment?
> 💡 *What the interviewer is looking for: Discussing API Versioning (URL path, header, or query param) and why you shouldn't just change existing DTO fields.*

### 🟥 Advanced

**Q5.** If you have a very large payload (thousands of items), would you still use a DTO? What alternatives exist?
> 💡 *What the interviewer is looking for: Streaming data or using paginated requests to avoid memory overhead of large DTO objects.*

### 🎯 Scenario-Based

**Q6.** The `GurutattvaAPI` needs to provide a "summary" vs. "full" version of a resource. How do you structure the DTOs to avoid code duplication while keeping the responses distinct?
> 💡 *What the interviewer is looking for: Inheritance or Composition in DTOs, or using different Request paths.*

### 💬 Behavioral

**Q7.** Tell me about a time a breaking change in a DTO caused a prod issue. How did you improve the process?
> 💡 *What the interviewer is looking for: Integration testing and contract-first approaches.*

---

## 8. Middleware & Filters

### ⬜ Beginner

**Q1.** What is the purpose of a custom Middleware in `GurutattvaAPI`? Give an example of one you might use.
> 💡 *What the interviewer is looking for: Intercepting every request/response (e.g., for logging or global exception handling).*

**Q2.** What is the difference between a `Middleware` and an `ActionFilter`?
> 💡 *What the interviewer is looking for: Middleware is part of the request pipeline; Filters are tied to specific controller actions and have access to MVC context.*

### 🟨 Intermediate

**Q3.** How would you implement a Global Exception Handler using Middleware to ensure the API always returns a consistent JSON error response?
> 💡 *What the interviewer is looking for: A `try-catch` block inside the Middleware that serializes the error and sets the HTTP status code.*

**Q4.** When would you choose an `ActionFilter` over `Middleware` for a specific logging requirement?
> 💡 *What the interviewer is looking for: When you need access to the `ActionExecutingContext` or specific route parameters.*

### 🟥 Advanced

**Q5.** In which order are Middleware executed? How do you ensure your `ExceptionHandlingMiddleware` is the very first one to catch everything?
> 💡 *What the interviewer is looking for: They run in the order they are registered in `Program.cs`. It must be registered first.*

### 🎯 Scenario-Based

**Q6.** You want to implement request-rate limiting only for "Authenticated" routes. How would you design this using a combination of Middleware and Attributes?
> 💡 *What the interviewer is looking for: Middleware that checks for a custom attribute or route metadata before checking cache/limits.*

### 💬 Behavioral

**Q7.** Describe a complex bug you found that was hidden inside a poorly implemented Middleware.
> 💡 *What the interviewer is looking for: Troubleshooting skills and understanding of the request lifecycle.*

---

## 9. Health Checks

### ⬜ Beginner

**Q1.** What is the standard endpoint for Health Checks in a .NET API, and why is it useful for deployments?
> 💡 *What the interviewer is looking for: `/health`, used by load balancers (AWS/Kubernetes) to verify if the pod is ready to serve traffic.*

**Q2.** Where are health check services registered in this solution?
> 💡 *What the interviewer is looking for: `Program.cs`.*

### 🟨 Intermediate

**Q3.** How do you add a custom health check to verify that the SQL database specifically is reachable by the `GurutattvaAPI`?
> 💡 *What the interviewer is looking for: Using `AddCheck<SqlHealthCheck>` or the built-in EF Core health check library.*

**Q4.** What is the difference between a "Liveness" check and a "Readiness" check?
> 💡 *What the interviewer is looking for: Liveness (is the app running at all?) vs. Readiness (is it ready to handle traffic, e.g., are the migrations finished?).*

### 🟥 Advanced

**Q5.** How would you configure a health check to monitor an external API dependency (like an AWS service) without slowing down the main health endpoint?
> 💡 *What the interviewer is looking for: Using background health check publishers or setting short timeouts.*

### 🎯 Scenario-Based

**Q6.** The `/health` endpoint is returning `Unhealthy`. You suspect it's the `MigrationSettings` check. If you disable the check, the API stays up, but queries fail. What does this tell you about your Health Check strategy?
> 💡 *What the interviewer is looking for: That the check was actually doing its job, but you need more granular logging to distinguish between DB connection issues and pending migrations.*

### 💬 Behavioral

**Q7.** Tell me about a time a Health Check saved your system from a cascading failure.
> 💡 *What the interviewer is looking for: Proactive monitoring experience.*

---

## 10. Serilog & structured logging

### ⬜ Beginner

**Q1.** What is "Structured Logging," and why is it better than just logging plain text?
> 💡 *What the interviewer is looking for: Logging data as JSON/objects, allowing for easier searching and filtering in tools like Seq or ELK.*

**Q2.** Where is the Serilog configuration usually defined in the `GurutattvaAPI`?
> 💡 *What the interviewer is looking for: `appsettings.json` or `Program.cs`.*

### 🟨 Intermediate

**Q3.** What is a "Correlation ID," and how would you implement it using Serilog to track a single request across multiple log entries?
> 💡 *What the interviewer is looking for: Using `LogContext.PushProperty` to attach a unique ID to every log line within a scope.*

**Q4.** How do you configure Serilog to write logs to both the Console and a File/Database simultaneously?
> 💡 *What the interviewer is looking for: Using multiple "Sinks".*

### 🟥 Advanced

**Q5.** How would you handle sensitive data (like PII) in your logs using Serilog to ensure compliance with privacy regulations?
> 💡 *What the interviewer is looking for: Using "Destructuring" policies to mask or exclude specific properties from logs.*

### 🎯 Scenario-Based

**Q6.** You are getting thousand of "Information" logs in production, causing disk space issues. How do you change the log level for just the `Microsoft.EntityFrameworkCore` namespace without redeploying the code?
> 💡 *What the interviewer is looking for: Modifying the `Logging` section in `appsettings.json` (or environment variables) to override the level for that specific category.*

### 💬 Behavioral

**Q7.** Describe a scenario where poor logging made it impossible to debug a critical issue. What changes did you implement afterwards?
> 💡 *What the interviewer is looking for: Iterative improvement based on post-mortem analysis.*

---

## 11. AWS Lambda Functions (AudioProcessor, ImageThumbnailProcessor)

### ⬜ Beginner

**Q1.** What are the two primary Lambda functions used in this project, and what are their responsibilities?
> 💡 *What the interviewer is looking for: Identifying `AudioProcessor` (media processing) and `ImageThumbnailProcessor` (image optimization).*

**Q2.** Why use AWS Lambda for these tasks instead of running them inside the main `GurutattvaAPI` project?
> 💡 *What the interviewer is looking for: Scalability and offloading resource-intensive tasks to keep the main API responsive.*

### 🟨 Intermediate

**Q3.** How does the `AudioProcessor` Lambda get triggered when a new file is uploaded?
> 💡 *What the interviewer is looking for: Mentioning S3 Event Triggers or SQS queues.*

**Q4.** What happens if the `ImageThumbnailProcessor` fails midway? How do you ensure the image eventually gets processed?
> 💡 *What the interviewer is looking for: Discussing Lambda retries, Dead Letter Queues (DLQ), and idempotent processing.*

### 🟥 Advanced

**Q5.** Discuss the "Cold Start" problem with .NET Lambda functions and how you might mitigate it for the `Gurutattva` processors.
> 💡 *What the interviewer is looking for: Native AOT compilation, Provisioned Concurrency, or keeping the functions "warm".*

### 🎯 Scenario-Based

**Q6.** A user uploads a 1GB MP3 file, and the `AudioProcessor` hits the Lambda execution timeout (15 minutes). How would you re-architect this to handle extremely large files?
> 💡 *What the interviewer is looking for: Breaking the task into smaller chunks, using AWS Step Functions, or moving to ECS Tasks for long-running jobs.*

### 💬 Behavioral

**Q7.** Why did you choose Lambda over a background service (like Hangfire or Worker Service) for image processing in this project?
> 💡 *What the interviewer is looking for: Cost-efficiency (pay-per-use) and independent scaling.*

---

## 12. Serverless architecture & async/background processing patterns

### ⬜ Beginner

**Q1.** What does "Serverless" mean in the context of the `LambdaFunctions` project?
> 💡 *What the interviewer is looking for: The developer doesn't manage the underlying servers; the cloud provider handles scaling and availability.*

**Q2.** What is the benefit of "Asynchronous Processing" for a feature like generating image thumbnails?
> 💡 *What the interviewer is looking for: The user doesn't have to wait for the thumbnail to be created before getting a successful "Upload" response.*

### 🟨 Intermediate

**Q3.** Explain the "Event-Driven Architecture" used to connect the `GurutattvaAPI` with the Lambda functions.
> 💡 *What the interviewer is looking for: The API performs an action (upload), an event is emitted (S3 Object Created), and the Lambda reacts to that event.*

**Q4.** How do you handle "at-least-once delivery" in a serverless environment to prevent duplicate processing of the same audio file?
> 💡 *What the interviewer is looking for: Implementing Idempotency checks (e.g., checking if the output file already exists or using a tracking table in the DB).*

### 🟥 Advanced

**Q5.** Compare SQS (Queue) and SNS (Topic) as communication channels for the `AudioProcessor`. When would you use one over the other?
> 💡 *What the interviewer is looking for: SQS for 1-to-1 reliable processing; SNS for 1-to-many broadcasting.*

### 🎯 Scenario-Based

**Q6.** The system is experiencing a sudden spike in traffic. Thousands of images are being uploaded. How does the serverless architecture prevent the database from being overwhelmed by the thumbnail output write operations?
> 💡 *What the interviewer is looking for: Using Reserved Concurrency on the Lambda to throttle processing or using SQS to buffer the load.*

### 💬 Behavioral

**Q7.** Describe a time you had to troubleshoot a failure that happened "silently" in a background process.
> 💡 *What the interviewer is looking for: Proficiency with CloudWatch logs and distributed tracing.*

---

## 13. Terraform & infrastructure-as-code (IaC)

### ⬜ Beginner

**Q1.** What is the purpose of the `terraform` folder in the `LambdaFunctions` project?
> 💡 *What the interviewer is looking for: Defining the infrastructure (Lambda settings, permissions, triggers) in code for repeatable deployments.*

**Q2.** What command would you run to see what changes Terraform is about to make to your AWS environment?
> 💡 *What the interviewer is looking for: `terraform plan`.*

### 🟨 Intermediate

**Q3.** How do you manage the "Terraform State" file? Why is it a bad idea to keep it on your local machine if you're working in a team?
> 💡 *What the interviewer is looking for: Using a "Remote Backend" (like S3/DynamoDB) to ensure consistency and prevent state locking issues.*

**Q4.** What is a "Terraform Module," and how could you use it to simplify the deployment of both the `AudioProcessor` and `ImageThumbnailProcessor`?
> 💡 *What the interviewer is looking for: Reusable code blocks for similar resources (Lambdas).*

### 🟥 Advanced

**Q5.** How would you handle AWS secrets (like database connection strings) in your Terraform scripts without hardcoding them?
> 💡 *What the interviewer is looking for: Using AWS Secrets Manager or System Manager Parameter Store, and referencing them in Terraform via data sources.*

### 🎯 Scenario-Based

**Q6.** You need to deploy a new version of the `AudioProcessor` to a "Staging" environment. How do you use Terraform to ensure the Staging infrastructure is identical to Production but with different resource names?
> 💡 *What the interviewer is looking for: Using Terraform "Workspaces" or distinct variable files (`.tfvars`) for each environment.*

### 💬 Behavioral

**Q7.** Why would you insist on using Terraform instead of just clicking "Create" in the AWS Management Console?
> 💡 *What the interviewer is looking for: Consistency, disaster recovery, and change tracking.*

---

## 14. MP3 to HLS conversion pipeline

### ⬜ Beginner

**Q1.** What is "HLS" (HTTP Live Streaming), and why conversion from MP3 to HLS is needed for a media API?
> 💡 *What the interviewer is looking for: HLS allows for adaptive bitrate streaming and better playback control over the web/mobile compared to raw MP3 files.*

**Q2.** Which project/tool in the solution is responsible for this conversion?
> 💡 *What the interviewer is looking for: `Mp3ToHlsConverter`.*

### 🟨 Intermediate

**Q3.** What role does "FFmpeg" typically play in a pipeline like `Mp3ToHlsConverter`?
> 💡 *What the interviewer is looking for: It's the underlying engine that does the heavy lifting of transcoding and segmenting the audio.*

**Q4.** Why is it important to generate a `.m3u8` playlist file during the conversion?
> 💡 *What the interviewer is looking for: It's the index file that tells the player where to find the individual `.ts` audio segments.*

### 🟥 Advanced

**Q5.** How would you optimize the `Mp3ToHlsConverter` to handle high-fidelity audio while minimizing the storage impact of multiple segments?
> 💡 *What the interviewer is looking for: Adjusting the segment duration and bitrate parameters during transcoding.*

### 🎯 Scenario-Based

**Q6.** The conversion pipeline is failing for some older MP3 files that have unusual bitrates. How would you modify the `Mp3ToHlsConverter` to handle these "corrupt" files gracefully?
> 💡 *What the interviewer is looking for: Adding a validation/pre-check step using `ffprobe` and catching conversion errors to log them separately.*

### 💬 Behavioral

**Q7.** Describe the most challenging media processing bug you've encountered.
> 💡 *What the interviewer is looking for: Technical depth and perseverance.*

---

## 15. User migration tooling & data migration strategies

### ⬜ Beginner

**Q1.** What is the purpose of the `UserMigrationTool` project?
> 💡 *What the interviewer is looking for: Migrating user data from an old system or a different database schema into the new Gurutattva database.*

**Q2.** Why run this as a separate utility instead of part of the main `GurutattvaAPI`?
> 💡 *What the interviewer is looking for: Performance (don't impact API traffic) and the one-off nature of migrations.*

### 🟨 Intermediate

**Q3.** When migrating users, how do you handle password migration if the old system used a different hashing algorithm than the new API?
> 💡 *What the interviewer is looking for: Discussing "Lazy Migration" (migrate hashes as they are and re-hash on first login) or specialized mapping logic.*

**Q4.** How do you ensure "Data Integrity" during a migration? What if some data is missing in the source?
> 💡 *What the interviewer is looking for: Validation steps, logging "skipped" records, and dry-run modes.*

### 🟥 Advanced

**Q5.** Discuss your strategy for a "Zero-Downtime Migration" for users. How do you keep the data in sync if users are still active on the old system while the migration is running?
> 💡 *What the interviewer is looking for: Using a "Dual Write" strategy or a Change Data Capture (CDC) mechanism.*

### 🎯 Scenario-Based

**Q6.** The `UserMigrationTool` has been running for 2 hours and migrated 50k users, but it crashes on record 50,001 due to an invalid email format. How do you resume without starting from scratch?
> 💡 *What the interviewer is looking for: Implementing "Checkpointing" or tracking "Last Processed ID" so the tool can pick up where it left off.*

### 💬 Behavioral

**Q7.** Tell me about a time a data migration went wrong. What was the recovery plan?
> 💡 *What the interviewer is looking for: Accountability and disaster recovery experience.*

---

## 16. GC tuning (GCLatencyMode.Batch) and performance optimization

### ⬜ Beginner

**Q1.** What does `GCSettings.LatencyMode = GCLatencyMode.Batch;` do when set on application startup in `GurutattvaAPI`?
> 💡 *What the interviewer is looking for: It tells the .NET Garbage Collector to prioritize throughput over responsiveness, which is often suitable for background processing or high-load APIs.*

**Q2.** Where is this GC setting typically applied in the solution?
> 💡 *What the interviewer is looking for: `Program.cs` (or `Global.asax` in older apps, but here it's `Program.cs`).*

### 🟨 Intermediate

**Q3.** Why would you choose `GCLatencyMode.Batch` for this specific API? What are the trade-offs regarding "Stop-the-world" pauses?
> 💡 *What the interviewer is looking for: It's good for batch migrations or heavy processing, but it can lead to longer GC pauses, potentially affecting API latency for short-lived requests.*

**Q4.** Apart from GC tuning, what is one other way you've optimized the performance of the `GurutattvaAPI` (e.g., using `Span<T>`, caching, or reducing allocations)?
> 💡 *What the interviewer is looking for: Experience with modern .NET performance features.*

### 🟥 Advanced

**Q5.** If you notice high CPU usage due to GC, how would you use `dotnet-counters` or `PerfView` to diagnose if `GCLatencyMode.Batch` is actually helping or hurting your specific workload?
> 💡 *What the interviewer is looking for: Knowledge of diagnostic tools and how to interpret GC metrics.*

### 🎯 Scenario-Based

**Q6.** The API is running in a memory-constrained container (e.g., 512MB RAM). Even with `GCLatencyMode.Batch`, you're seeing `OutOfMemoryException` during user migrations. What's your next move?
> 💡 *What the interviewer is looking for: Suggesting `GCSettings.LargeObjectHeapCompactionMode = GCLOHCompactionMode.CompactOnce;` or analyzing memory dumps for leaks.*

### 💬 Behavioral

**Q7.** Describe a time you had to justify a "low-level" performance optimization to a product owner who didn't understand why it mattered.
> 💡 *What the interviewer is looking for: Communication skills and linking technical metrics to business outcomes (e.g., cost savings or user experience).*

---

## 17. Auto-migration on startup — risks, safeguards, and alternatives

### ⬜ Beginner

**Q1.** What is the purpose of the `MigrationSettings.EnableAutoMigration` flag?
> 💡 *What the interviewer is looking for: Allowing the application to automatically apply pending EF Core migrations when it starts up.*

**Q2.** Where would you define this flag so it can be different between Development and Production?
> 💡 *What the interviewer is looking for: `appsettings.json` and `appsettings.Production.json`.*

### 🟨 Intermediate

**Q3.** What are the primary risks of running `context.Database.Migrate()` automatically in a Production environment with multiple API instances?
> 💡 *What the interviewer is looking for: Race conditions where multiple instances try to modify the schema at once, potentially causing failures or corruption.*

**Q4.** How do you implement a "Guard Clause" to ensure migrations only run if certain conditions are met (e.g., only in a specific environment)?
> 💡 *What the interviewer is looking for: Checking the `IHostEnvironment` or the custom settings flag before calling the migrate method.*

### 🟥 Advanced

**Q5.** Propose an alternative to "Auto-migration on Startup" for a high-availability production environment.
> 💡 *What the interviewer is looking for: Using a "Migration Script" generated via `dotnet ef migrations script` and executed by a CI/CD pipeline (e.g., GitHub Actions or Azure DevOps) before the app deployment.*

### 🎯 Scenario-Based

**Q6.** A migration fails halfway through on startup. The database is now in an inconsistent state. How do you prevent the API from starting up and serving traffic while the DB is "broken"?
> 💡 *What the interviewer is looking for: Ensuring the migration logic is inside a `try-catch` that logs the error and exits the application with a non-zero code, preventing the container from becoming "Healthy".*

### 💬 Behavioral

**Q7.** When have you decided that "Auto-migration" was no longer safe for a project? What was the "tipping point"?
> 💡 *What the interviewer is looking for: Realizing the complexity of the schema or the scale of the user base necessitated a more controlled process.*

---

## 18. appsettings.json environment configuration (Development vs Production)

### ⬜ Beginner

**Q1.** Which file is used to store configuration values that are specific to the Production environment in `GurutattvaAPI`?
> 💡 *What the interviewer is looking for: `appsettings.Production.json`.*

**Q2.** How does the .NET runtime know which `appsettings` file to load?
> 💡 *What the interviewer is looking for: It checks the `ASPNETCORE_ENVIRONMENT` environment variable.*

### 🟨 Intermediate

**Q3.** What happens if a setting is defined in `appsettings.json` but NOT in `appsettings.Production.json` when running in Prod?
> 💡 *What the interviewer is looking for: It "falls back" to the value in the base `appsettings.json` file.*

**Q4.** Why should you avoid storing sensitive secrets (like API keys or DB passwords) directly in `appsettings.Production.json`?
> 💡 *What the interviewer is looking for: Security; these files are often checked into source control. Secrets should be in environment variables or a Secret Manager.*

### 🟥 Advanced

**Q5.** How would you implement a custom `ConfigurationProvider` to load settings from a database or a remote configuration service (like Azure App Configuration) into the `GurutattvaAPI`?
> 💡 *What the interviewer is looking for: Inheriting from `ConfigurationProvider` and `IConfigurationSource` and registering it in `Program.cs`.*

### 🎯 Scenario-Based

**Q6.** You accidentally deployed the `ASPNETCORE_ENVIRONMENT=Development` setting to Production. What are the security implications for a `Gurutattva` user?
> 💡 *What the interviewer is looking for: Detailed error pages (Developer Exception Page) might leak internal stack traces and server info to attackers.*

### 💬 Behavioral

**Q7.** Tell me about a time you had to manage a complex configuration matrix across 5+ environments. How did you stay organized?
> 💡 *What the interviewer is looking for: Use of templates, CI/CD variables, or centralized config management.*

---

## 19. Testing strategy — unit, integration, and what's hard to test

### ⬜ Beginner

**Q1.** What is the main difference between a "Unit Test" and an "Integration Test" in this architecture?
> 💡 *What the interviewer is looking for: Unit tests test isolated logic (e.g., a single service method with mocks); Integration tests test the interaction between layers/components (e.g., API -> Service -> DB).*

**Q2.** Which layer (Domain, Application, Infrastructure) is the easiest to test with pure Unit Tests?
> 💡 *What the interviewer is looking for: The Domain layer, because it has no external dependencies.*

### 🟨 Intermediate

**Q3.** When testing a Use Case in the `Application` layer, how do you handle the dependency on the `Infrastructure` repository?
> 💡 *What the interviewer is looking for: Using a Mocking framework (like Moq or NSubstitute) to simulate the repository's behavior.*

**Q4.** What is "WebApplicationFactory," and how would you use it to write integration tests for your `GurutattvaAPI` controllers?
> 💡 *What the interviewer is looking for: It allows you to bootstrap the entire API in-memory for end-to-end testing of endpoints.*

### 🟥 Advanced

**Q5.** Why are AWS Lambda functions like the `AudioProcessor` difficult to test locally? How do you overcome this?
> 💡 *What the interviewer is looking for: Dependency on cloud-native triggers/services (S3, SQS). Solution: Use local emulators (LocalStack) or design the Lambda to be a thin wrapper around a testable Application service.*

### 🎯 Scenario-Based

**Q6.** You want to test the `UserMigrationTool` without actually modifying the Production database. How do you design the tool to support a "Dry Run" mode?
> 💡 *What the interviewer is looking for: Injecting a "Persistence Strategy" interface that can be switched between a real DB and a "Null/Logging" implementation.*

### 💬 Behavioral

**Q7.** Describe your philosophy on Code Coverage. Is 100% always the goal?
> 💡 *What the interviewer is looking for: Pragmatism; focusing on high-risk/high-value areas rather than just hitting a number.*

---

## 20. Security — API authentication, authorization, secrets management

### ⬜ Beginner

**Q1.** What is the difference between "Authentication" and "Authorization" in the `GurutattvaAPI`?
> 💡 *What the interviewer is looking for: Authentication is identifying who the user is; Authorization is determining what they are allowed to do.*

**Q2.** How should you securely store the database connection string for the `Infrastructure` project on your local machine?
> 💡 *What the interviewer is looking for: Using .NET "User Secrets" utility.*

### 🟨 Intermediate

**Q3.** If the `GurutattvaAPI` uses JWT (JSON Web Tokens), where is the best place to validate the token—Middleware or a Filter?
> 💡 *What the interviewer is looking for: Middleware (the standard `UseAuthentication` / `UseAuthorization` pipeline) is preferred for global validity.*

**Q4.** How do you implement "Role-Based Access Control" (RBAC) to ensure only "Admin" users can access the `UserMigrationTool` logic if it were exposed via an API?
> 💡 *What the interviewer is looking for: Using the `[Authorize(Roles = "Admin")]` attribute or Policy-based authorization.*

### 🟥 Advanced

**Q5.** Discuss how you would implement "Refresh Tokens" for the `GurutattvaAPI`. Where would you store them, and why?
> 💡 *What the interviewer is looking for: Storing them in a secure database with an expiry, distinct from the (short-lived) Access Token, to allow users to stay logged in securely.*

### 🎯 Scenario-Based

**Q6.** A security audit reveals that your API is vulnerable to "SQL Injection" despite using EF Core. How is this possible, and where would you look in the `Infrastructure` project for the leak?
> 💡 *What the interviewer is looking for: Using `FromSqlInterpolated` or `FromSqlRaw` with string concatenation instead of parameterized queries.*

### 💬 Behavioral

**Q7.** Tell me about a time you found a security vulnerability in a project you were working on. How did you handle the disclosure and the patch?
> 💡 *What the interviewer is looking for: Professionalism and a security-first mindset.*

---

## 🧠 Rapid Fire (Yes/No or One-Liner Answers)

1. **Does the Domain layer depend on the Application layer?** (No)
2. **Can a Domain Entity contain business logic?** (Yes, and it should!)
3. **Is FluentValidation part of the .NET Core framework?** (No, it's a third-party library)
4. **What does DI stand for?** (Dependency Injection)
5. **Does `GCLatencyMode.Batch` reduce the frequency of GC?** (Yes, but it increases the duration)
6. **Can Terraform manage resources in Azure too?** (Yes, it's cloud-agnostic)
7. **Is a DTO the same as a Value Object?** (No, DTOs carry data; VOs have logic/validations)
8. **Should I put a `try-catch` in every Controller action?** (No, use Global Exception Middleware)
9. **Is `AddScoped` thread-safe?** (It depends on the service, but it's scoped to one request/thread)
10. **Does EF Core support NoSql?** (Yes, e.g., Azure Cosmos DB provider)
11. **Should I commit `appsettings.json` to Git?** (Yes, but not the secrets!)
12. **Is HLS better for mobile than raw MP3?** (Yes, due to adaptive streaming)
13. **Can AWS Lambda run C# code?** (Yes, natively)
14. **Does Clean Architecture mandate the use of MediatR?** (No, it's optional but common)
15. **What is the command to create a new EF Migration?** (`dotnet ef migrations add`)
16. **Is Serilog only for Console logging?** (No, it has "Sinks" for everything)
17. **Can a single solution have multiple `DbContext` classes?** (Yes)
18. **Should a Unit Test hit the real database?** (No, that's an integration test)
19. **What is the default port for HTTP?** (80)
20. **Is the Gurutattva API a REST or GraphQL API based on the overview?** (REST)
