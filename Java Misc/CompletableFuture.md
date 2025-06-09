# CompletableFuture in Java

`CompletableFuture` is a class introduced in Java 8 as part of the `java.util.concurrent` package that represents a future result of an asynchronous computation. It provides a powerful and flexible way to handle asynchronous programming in Java.

## Key Features

1. **Asynchronous Execution**: Allows you to run tasks in background threads
2. **Chaining**: Enables combining multiple asynchronous operations
3. **Composition**: Supports combining multiple futures together
4. **Exception Handling**: Provides mechanisms to handle exceptions in async workflows
5. **Manual Completion**: Allows manually completing the future with a value or exception

## Basic Usage

### Creating a CompletableFuture

```java
// Create a completed future
CompletableFuture<String> completedFuture = CompletableFuture.completedFuture("Hello");

// Run async task (no return value)
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
    // Perform some computation
});

// Supply async (with return value)
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "Result";
});
```

### Chaining Operations

```java
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")  // transform result
    .thenAccept(System.out::println); // consume result
```

## Common Methods

### Transformation Methods
- `thenApply()` - transforms the result synchronously
- `thenApplyAsync()` - transforms the result asynchronously
- `thenCompose()` - chains another CompletableFuture (flatMap)

### Callback Methods
- `thenAccept()` - consumes the result
- `thenRun()` - runs a Runnable after completion

### Combination Methods
- `thenCombine()` - combines two independent futures
- `thenAcceptBoth()` - consumes results of two futures
- `allOf()` - waits for all futures to complete
- `anyOf()` - waits for any future to complete

### Exception Handling
- `exceptionally()` - provides a fallback value
- `handle()` - handles both success and failure cases
- `whenComplete()` - similar to handle but doesn't transform

## Example: Chaining Multiple Operations

```java
CompletableFuture.supplyAsync(() -> {
    // Simulate long computation
    try { Thread.sleep(1000); } catch (InterruptedException e) {}
    return 20;
})
.thenApply(i -> i * 2) // 40
.thenApplyAsync(i -> i + 5) // 45 (runs in different thread)
.exceptionally(ex -> { // if any error occurs
    System.err.println("Error: " + ex);
    return 0;
})
.thenAccept(System.out::println); // prints 45
```

## Example: Combining Futures

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");

future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2)
       .thenAccept(System.out::println); // "Hello World"
```

## Best Practices

1. Always handle exceptions in CompletableFuture chains
2. Prefer `*Async` methods for long-running operations to avoid blocking
3. Use custom Executors for better control over thread pools
4. Be careful with thread-local variables in async operations
5. Consider timeouts with `orTimeout()` or `completeOnTimeout()`

`CompletableFuture` provides a modern, functional approach to asynchronous programming in Java, making it easier to write non-blocking, concurrent code.



`thenCompose()` and `thenApply()` are both methods in Java's **CompletableFuture**, but they serve different purposes when chaining asynchronous operations.

### Key Differences:

1. `thenApply()`:
    
    - Used when you want to transform the result of a `CompletableFuture`.
        
    - Returns a **new CompletableFuture** wrapping the transformed result.
        
    - Can lead to **nested futures** (`CompletableFuture<CompletableFuture<T>>`).
        
    
    **Example:**
    
    java
    
    ```
    CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> 10)
        .thenApply(num -> num * 2); // Transforms result
    ```
    
2. `thenCompose()`:
    
    - Used when the next computation itself returns a `CompletableFuture`.
        
    - **Flattens** the result, avoiding nested futures.
        
    - Ideal for chaining dependent asynchronous calls.
        
    
    **Example:**
    
    java
    
    ```
    CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> 10)
        .thenCompose(num -> CompletableFuture.supplyAsync(() -> num * 2)); // Chains async calls
    ```
    

### When to Use:

- Use `thenApply()` when you have a **synchronous transformation**.
    
- Use `thenCompose()` when you need to **chain asynchronous operations** without nesting futures.
    

# Difference Between `CompletableFuture` and `Future` in Java

Both `CompletableFuture` and `Future` are part of Java's concurrency API, but they serve different purposes and have significant differences in functionality.

## `Future` (java.util.concurrent.Future)

**Introduced in Java 5**, `Future` represents the result of an asynchronous computation with basic capabilities:

### Key Characteristics:
1. **Basic Asynchronous Result Container**
   - Can check if computation is done (`isDone()`)
   - Can cancel the computation (`cancel()`)
   - Can block and get the result (`get()`)

2. **Limitations:**
   - No completion notification (you must poll with `isDone()`)
   - No chaining/composition of async operations
   - No exception handling built into the API
   - Cannot manually complete the future
   - No support for combining multiple futures

### Example Usage:
```java
ExecutorService executor = Executors.newFixedThreadPool(2);
Future<String> future = executor.submit(() -> {
    Thread.sleep(1000);
    return "Result";
});

// Block and get result (throws checked exceptions)
String result = future.get(); 
```

## `CompletableFuture` (java.util.concurrent.CompletableFuture)

**Introduced in Java 8**, `CompletableFuture` is an enhanced future with many additional features:

### Key Advantages:
1. **Non-blocking Completion Handling**
   - Callbacks via `thenApply()`, `thenAccept()`, `thenRun()`
   - No need to manually check `isDone()`

2. **Composition & Chaining**
   - Chain multiple async operations (`thenCompose()`)
   - Combine multiple futures (`thenCombine()`, `allOf()`)

3. **Exception Handling**
   - Built-in methods like `exceptionally()`, `handle()`

4. **Manual Completion Control**
   - Can complete manually (`complete()`, `completeExceptionally()`)

5. **Async Variants**
   - Methods with `Async` suffix run in different threads

6. **Functional Programming Style**
   - Works well with lambdas and method references

### Example Usage:
```java
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")
    .thenApplyAsync(String::toUpperCase)
    .exceptionally(ex -> "Error occurred")
    .thenAccept(System.out::println);
```

## Key Differences Table

| Feature                | `Future`                      | `CompletableFuture`                  |
|------------------------|-------------------------------|---------------------------------------|
| **Completion Callback** | Not supported                 | Supported via `then*()` methods      |
| **Chaining**           | Not supported                 | Supported                            |
| **Combining Futures**  | Not supported                 | Supported (`allOf`, `anyOf`, etc.)   |
| **Exception Handling** | Throws ExecutionException     | Built-in methods (`exceptionally`)   |
| **Manual Completion**  | Not possible                  | Supported                            |
| **Async Operations**   | Only via ExecutorService      | Built-in async methods               |
| **Non-blocking**       | Only blocking `get()`         | Fully non-blocking possible          |
| **Java Version**       | Since Java 5                  | Since Java 8                         |

## When to Use Which

- Use **`Future`** when:
  - You need simple asynchronous execution
  - You're working with legacy code
  - You only need basic "fire and forget" with result checking

- Use **`CompletableFuture`** when:
  - You need to chain async operations
  - You want non-blocking completion handling
  - You need to combine multiple async results
  - You want better exception handling
  - You're using Java 8 or later

`CompletableFuture` is essentially a more powerful, modern replacement for `Future` in most use cases, providing a functional approach to asynchronous programming.