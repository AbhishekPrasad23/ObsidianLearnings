
Command Query Responsibility Segregation (CQRS) is a design pattern that segregates read and write operations for a data store into separate data models. This approach allows each model to be optimized independently and can improve the performance, scalability, and security of an application.

[](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs#context-and-problem)

## Context and problem

In a traditional architecture, a single data model is often used for both read and write operations. This approach is straightforward and is suited for basic create, read, update, and delete (CRUD) operations.

[![Diagram that shows a traditional CRUD architecture.](https://learn.microsoft.com/en-us/azure/architecture/patterns/_images/command-and-query-responsibility-segregation-cqrs-tradition-crud.png)](https://learn.microsoft.com/en-us/azure/architecture/patterns/_images/command-and-query-responsibility-segregation-cqrs-tradition-crud.png#lightbox)

As applications grow, it can become increasingly difficult to optimize read and write operations on a single data model. Read and write operations often have different performance and scaling requirements. A traditional CRUD architecture doesn't take this asymmetry into account, which can result in the following challenges:

- **Data mismatch:** The read and write representations of data often differ. Some fields that are required during updates might be unnecessary during read operations.
    
- **Lock contention:** Parallel operations on the same data set can cause lock contention.
    
- **Performance problems:** The traditional approach can have a negative effect on performance because of load on the data store and data access layer, and the complexity of queries required to retrieve information.
    
- **Security challenges:** It can be difficult to manage security when entities are subject to read and write operations. This overlap can expose data in unintended contexts.
    

Combining these responsibilities can result in an overly complicated model.

[](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs#solution)

## Solution

Use the CQRS pattern to separate write operations, or _commands_, from read operations, or _queries_. Commands update data. Queries retrieve data. The CQRS pattern is useful in scenarios that require a clear separation between commands and reads.

- **Understand commands.** Commands should represent specific business tasks instead of low-level data updates. For example, in a hotel-booking app, use the command "Book hotel room" instead of "Set ReservationStatus to Reserved." This approach better captures the intent of the user and aligns commands with business processes. To help ensure that commands are successful, you might need to refine the user interaction flow and server-side logic and consider asynchronous processing.