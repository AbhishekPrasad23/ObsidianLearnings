
| 1   | [[#The 4 Pillars of Object-Oriented Programming (OOP) - Brief Overview]] |
| --- | ------------------------------------------------------------------------ |
| 2   | [[#**Method Hiding in Java Purpose & Usage**]]                           |
| 3   | [[#**Method Hiding vs. Method Overriding**]]                             |
| 4   | [[#CascadeType.ALL vs orphanRemoval = true]]                             |
| 5   | [[#Sealed Classes]]                                                      |
| 6   | [[#Java Collections tricks]]                                             |
| 7   | [[#@JsonIgnore vs @Transient]]                                           |
| 8   | [[#Reflection API]]                                                      |
| 9   | [[#Stream Gatherer's]]                                                   |
|     |                                                                          |
|     |                                                                          |



# The 4 Pillars of Object-Oriented Programming (OOP) - Brief Overview

## 1. **Encapsulation**
- Bundling data (attributes) and methods (functions) that operate on the data into a single unit (class)
- Restricts direct access to some components through access modifiers (private, protected, public)
- Example: 
  ```java
  class BankAccount {
      private double balance;  // Hidden data
      
      public void deposit(double amount) {  // Controlled access
          if(amount > 0) balance += amount;
      }
  }
  ```

## 2. **Abstraction**
- Hiding complex implementation details and showing only essential features
- Achieved through abstract classes and interfaces
- Example:
  ```java
  abstract class Vehicle {
      abstract void start();  // What to do, not how
  }
  ```

## 3. **Inheritance**
- Creating new classes (child/derived) from existing ones (parent/base)
- Promotes code reusability and establishes "is-a" relationships
- Example:
  ```java
  class Car extends Vehicle {  // Car inherits from Vehicle
      void start() { System.out.println("Car starts"); }
  }
  ```

## 4. **Polymorphism**
- "Many forms" - ability to present the same interface for different underlying forms
- Two types:
  - **Compile-time** (Method overloading)
    ```java
    void print(int i) { ... }
    void print(String s) { ... }
    ```
  - **Runtime** (Method overriding)
    ```java
    Animal a = new Dog();
    a.sound();  // Calls Dog's implementation
    ```

These pillars work together to create flexible, maintainable, and scalable software systems.
## Key Rules for Abstract Methods in Interfaces

1. **Implicit Modifiers**:
    
    - All methods are `public abstract` by default
        
    - All fields are `public static final` by default
        
2. **Implementation Requirements**:
    
    - Concrete classes must implement all abstract methods
        
    - Or declare themselves abstract
        
3. **Multiple Inheritance**:
    
    - A class can implement multiple interfaces
        
    - Must implement all abstract methods from all interfaces


Interview Questions -✅ Q: Why is String immutable and final in Java?  
  
✅ Interview Answer (Simple Spoken Style):  
In Java, String is immutable, meaning once we create a string object, we cannot change its value.  
  
And the String class is also marked as final, so it cannot be extended or modified by other classes.  
  
There are a few strong reasons for this:  
  
🔹 1. Security  
▪️ Strings are used in many sensitive areas like file paths, network connections, or database URLs.  
▪️ If Strings were mutable, someone could change the value after it's created, which would be unsafe.  
  
🔹 2. Caching and Memory Efficiency  
▪️ Java uses a String pool in memory.  
▪️ If Strings were mutable, it would be risky to share them between multiple places.  
▪️ Because they are immutable, we can safely reuse the same string object from the pool.  
  
🔹 3. Thread Safety  
▪️ Since strings can’t be changed, they are automatically thread-safe.  
▪️ Multiple threads can use the same string without synchronization.  
  
🔹 4. Hashcode Caching  
▪️ Strings are often used as keys in HashMaps.  
▪️ Since the value never changes, the hashcode also stays the same — which makes lookup fast and consistent.  
  
✅ Final Summary Line:  
So, to make Strings secure, efficient, thread-safe, and reliable, Java designers made the String class immutable and final.


# **Method Hiding in Java: Purpose & Usage**

In Java, **method hiding** occurs when a subclass defines a **static method** with the **same signature** as a static method in its superclass. Unlike method overriding (for instance methods), method hiding does not involve polymorphism and is resolved at **compile-time** based on the reference type.

---

## **How Method Hiding Works**
### **1. Key Characteristics**
- Applies **only to static methods** (instance methods use overriding).
- The **subclass method "hides"** the superclass method (does not override it).
- Method resolution is done at **compile-time** (static binding).

### **2. Example**
```java
class Parent {
    public static void display() {
        System.out.println("Parent's static method");
    }
}

class Child extends Parent {
    public static void display() {  // Hides Parent.display()
        System.out.println("Child's static method");
    }
}

public class Main {
    public static void main(String[] args) {
        Parent p = new Parent();
        p.display(); // Output: Parent's static method

        Child c = new Child();
        c.display(); // Output: Child's static method

        Parent p2 = new Child();
        p2.display(); // Output: Parent's static method (not Child's!)
    }
}
```
**Output:**
```
Parent's static method
Child's static method
Parent's static method
```
- Even when `p2` refers to a `Child` object, `Parent.display()` is called because **static methods are resolved by reference type, not runtime object type**.

---

## **Purpose of Method Hiding**
### **1. Provide Class-Specific Static Behavior**
- Allows subclasses to **define their own static utility methods** without affecting the superclass.
- Useful in **utility classes** (e.g., `Math`, `Collections`).

### **2. Avoid Confusion in Inheritance**
- Prevents accidental overriding of static methods (since Java enforces hiding instead).
- Makes it clear that static methods **do not support runtime polymorphism**.

### **3. Better Code Organization**
- Helps maintain **separate static logic** for different classes in an inheritance hierarchy.

---

## **Method Hiding vs. Method Overriding**
| Feature | Method Hiding (Static) | Method Overriding (Instance) |
|---------|----------------------|---------------------------|
| **Binding** | Compile-time (static) | Runtime (dynamic) |
| **Applicable to** | `static` methods | Non-`static` methods |
| **Polymorphism** | No (depends on reference type) | Yes (depends on object type) |
| **`@Override`** | Not allowed (compile error) | Allowed (recommended) |

### **Example of Overriding (For Comparison)**
```java
class Parent {
    public void show() {  // Instance method
        System.out.println("Parent's instance method");
    }
}

class Child extends Parent {
    @Override
    public void show() {  // Overrides Parent.show()
        System.out.println("Child's instance method");
    }
}

public class Main {
    public static void main(String[] args) {
        Parent p = new Child();
        p.show(); // Output: Child's instance method (runtime polymorphism)
    }
}
```
**Output:**  
`Child's instance method` (due to dynamic method dispatch).

---

## **Best Practices**
✔ **Avoid hiding static methods** if possible (can be confusing).  
✔ Use **`@Override` for instance methods** (prevents hiding by mistake).  
✔ Prefer **composition over inheritance** when dealing with static utilities.  

---

## **Conclusion**
- **Method hiding** applies only to `static` methods.  
- The **decision of which method to call** is made at **compile-time** (unlike overriding).  
- Useful for **class-level utilities**, but can lead to confusion if misused.  

Would you like a real-world use case where method hiding is beneficial? 😊

String.valueOf() vs toString() in Java — What’s the Difference?  
In Java, both convert values to String — but there are subtle differences that matter in real projects.  
🔹 1. Null Safety  
String.valueOf() → Never throws NullPointerException  
toString() → Throws NullPointerException if the object is null  
java  
CopyEdit  
Integer obj = null;  
  
System.out.println(String.valueOf(obj)); // Output: "null"  
System.out.println(obj.toString()); // ❌ NullPointerException  
🔹 2. Works on Primitives  
String.valueOf() → Directly works with primitives (int, double, boolean, etc.)  
toString() → Works only on objects  
java  
CopyEdit  
int num = 100;  
  
System.out.println(String.valueOf(num)); // ✅ "100"  
System.out.println(Integer.toString(num)); // ✅ "100" (primitive overload)  
🔹 3. Internally Related  
String.valueOf(obj) internally calls obj.toString() after null-check  
So performance is almost the same  
✅ When to Use?  
Use String.valueOf() → When there’s a chance of null (safe choice)  
Use .toString() → When you’re 100% sure the object is non-null (slightly shorter)


Spring Boot Face-off: @Component vs @Configuration  
  
If you’ve worked with Spring, you’ve probably used both — but do you know when to choose which?  
🟢 @Component  
• Marks a class as a Spring-managed bean  
• Discovered automatically via component scanning  
• Best for services, repositories, helpers  
  
🔵 @Configuration  
• A specialized @Component for bean definitions  
• Works with @Bean methods  
• Ensures singleton instances by default (even across multiple method calls)  
  
⚡ Quick takeaway:  
• Use @Component → when your class is itself the bean  
• Use @Configuration → when your class produces other beans  
💭 What’s your go-to choice when setting up beans?  
  




---

### 🆚 Criteria API vs JPQL

|Feature|Criteria API|JPQL|
|---|---|---|
|**Type**|Programmatic|Declarative (String-based)|
|**Syntax**|Uses Java objects and method chaining|Uses SQL-like query strings|
|**Compile-time Safety**|✅ Type-safe (checked at compile time)|❌ Prone to runtime errors due to string parsing|
|**Dynamic Query Support**|✅ Easier to build dynamic queries|❌ Harder to modify dynamically|
|**Readability**|❌ Verbose and harder to read|✅ More concise and familiar to SQL users|
|**Use Case**|Preferred for complex, dynamic queries|Ideal for simple, static queries|

---

Great question—this is a common topic in JPA/Hibernate interviews, and understanding the distinction can really sharpen your backend development skills.

---
# CascadeType.ALL vs orphanRemoval = true
### 🔄 CascadeType.ALL

- **Purpose**: Automatically propagates all JPA operations (like `persist`, `merge`, `remove`, `refresh`, `detach`) from a parent entity to its child entities.
- **Use Case**: When you want changes to the parent to affect the child. For example, saving a parent will also save its children.
- **Example**:
    
    ```java
    @OneToMany(cascade = CascadeType.ALL)
    private List<OrderItem> items;
    ```
    
    If you call `entityManager.remove(order)`, all `OrderItem`s will also be removed.

---

### 🧹 orphanRemoval = true

- **Purpose**: Automatically deletes child entities that are no longer referenced by the parent.
- **Use Case**: When a child is removed from the parent’s collection, it should also be deleted from the database.
- **Example**:
    
    ```java
    @OneToMany(orphanRemoval = true)
    private List<OrderItem> items;
    ```
    
    If you do `order.getItems().remove(item)`, that `item` will be deleted from the database.

---

### 🧠 Key Differences

|Feature|`CascadeType.ALL`|`orphanRemoval = true`|
|---|---|---|
|**Triggers**|JPA operations on parent|Removal from parent’s collection|
|**Effect**|Propagates operations|Deletes unreferenced children|
|**Scope**|Broad (all operations)|Specific (removal only)|
|**Common Together?**|Yes, often used together|Complements cascading deletes|

---

### ✅ Best Practice

Use both together when:

- You want full lifecycle management of child entities.
- You want to avoid manual deletion of orphans.

Let me know if you’d like a real-world example or a diagram to visualize this!



---

### 🟦 Blue-Green Deployment

- **Concept**: Maintain two identical environments—**Blue** (current live) and **Green** (new version).
- **Process**:
    - Deploy the new version to the Green environment.
    - Run tests and validations.
    - Switch traffic from Blue to Green once everything looks good.
- **Benefits**:
    - Instant rollback: just switch back to Blue if issues arise.
    - Zero downtime during deployment.
- **Drawbacks**:
    - Requires double infrastructure (more cost).
    - Complex traffic routing setup.

---

### 🔄 Rolling Deployment

- **Concept**: Gradually replace old version with new version across servers or containers.
- **Process**:
    - Update a few instances at a time.
    - Monitor for issues before proceeding to the next batch.
- **Benefits**:
    - Lower resource usage—no need for duplicate environments.
    - Smooth transition with minimal disruption.
- **Drawbacks**:
    - Rollback is slower and more complex.
    - Temporary inconsistency: some users may hit old version while others see the new one.

---

### 🧠 Summary Table

|Feature|Blue-Green Deployment|Rolling Deployment|
|---|---|---|
|**Downtime**|None|Minimal|
|**Rollback**|Instant|Gradual|
|**Infrastructure**|Requires duplicate|Uses existing|
|**Traffic Control**|Manual switch|Automatic rotation|
|**Consistency**|Fully consistent|Temporarily mixed|

---

<<<<<<< HEAD
Your JVM has 4GB of heap. Your app can only use about 2.5GB. Here's where the rest goes.  
  
Most developers think JVM memory = heap. It's not. The heap is just one piece.  
  
a) Metaspace → stores class metadata. Every Spring bean, every Hibernate proxy loads classes here. 150-250MB for a typical Spring Boot app. No cap by default.  
  
b) Thread Stacks → each thread = 1MB. 200 threads from Tomcat + HikariCP + Kafka + @Async = 200MB. Not on the heap. Invisible to heap monitoring.  
  
c) Direct Buffers → WebClient, Kafka client, Netty allocate memory outside the heap. Won't show in heap dumps. Won't trigger GC.  
  
d) Code Cache → JIT compiled code. 50-150MB. Grows as your app warms up.  
  
e) GC Overhead → garbage collector needs its own working memory on top of the heap.  
  
Here's where it gets dangerous.  
  
You set -Xmx=4g. Container memory limit = 4GB. Looks perfect.  
  
Reality: 4GB heap + 200MB metaspace + 200MB threads + 100MB buffers + 100MB code cache = ~4.7GB.  
  
Container has 4GB. Kubernetes OOMKills your pod. You check the heap. Heap was fine. 2.5GB used. But the pod is dead. Because OOMKill is about total process memory, not just the heap.  
  
The fix:  
→ Heap should be 70-75% of container memory. Not 100%.  
→ -Xmx=4g needs at least 5.2GB container. Or set -Xmx=3g in 4GB container.  
→ Cap metaspace: -XX:MaxMetaspaceSize=256m  
→ Cap direct buffers: -XX:MaxDirectMemorySize=256m  
→ Monitor non-heap: /actuator/metrics/jvm.memory.used?tag=area:nonheap  
  
OOMKilled doesn't always mean your app used too much memory. Sometimes it means the JVM did.
=======
>>>>>>> 314900666ff55ba79d92cbfa980259ae986504b2


---
# Sealed Classes

## What is a Sealed Class in Java?

A sealed class restricts which other classes or interfaces can inherit from it. This ensures a controlled and predictable inheritance hierarchy.

**Key Benefits**

- Provides controlled inheritance
- Enhances security and maintainability
- Improves code readability
- Enables better pattern matching and exhaustiveness checks
- Prevents unauthorized subclassing

## **Syntax**

All permitted subclasses must be declared as final, sealed, or non-sealed and should reside in the same module or package.

sealed class Vehicle permits Car, Truck, Bike {  
}

- sealed restricts inheritance.
- permits specifies the allowed subclasses.
- Only the listed classes can extend Vehicle.

Basic Sealed Class

sealed class Vehicle permits Car, Truck {  
  
    void start() {  
        System.out.println("Vehicle is starting...");  
    }  
}  
  
final class Car extends Vehicle {  
}  
  
final class Truck extends Vehicle {  
}  
  
public class TestSealed {  
    public static void main(String[] args) {  
        Vehicle v = new Car();  
        v.start();  
    }  
}

Output

Vehicle is starting...

## **Permitted Subclass Modifiers**

Every subclass of a sealed class must declare one of the following modifiers.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:875/1*H84d1RrGCNgdzQj3KJGc1g.png)

## Using sealed, final, and non-sealed

sealed class Shape permits Circle, Rectangle, Triangle {  
}  
  
// Cannot be extended further  
final class Circle extends Shape {  
}  
  
// Allows only specific subclasses  
sealed class Rectangle extends Shape permits Square {  
}  
  
final class Square extends Rectangle {  
}  
  
// Allows unrestricted inheritance  
non-sealed class Triangle extends Shape {  
}  
  
class RightTriangle extends Triangle {  
}

## Sealed Interfaces

Sealed behaviour can also be applied to interfaces.

sealed interface Payment permits CreditCard, PayPal, Cash {  
}  
  
final class CreditCard implements Payment {  
}  
  
final class PayPal implements Payment {  
}  
  
final class Cash implements Payment {  
}

Sealed interfaces are widely used in designing secure APIs and domain models.

Go through this real-world example and try to get the output.

sealed class Employee permits Manager, Developer, Intern {  
    abstract void work();  
}  
  
final class Manager extends Employee {  
    void work() {  
        System.out.println("Managing the team.");  
    }  
}  
  
final class Developer extends Employee {  
    void work() {  
        System.out.println("Writing code.");  
    }  
}  
  
final class Intern extends Employee {  
    void work() {  
        System.out.println("Learning and assisting.");  
    }  
}  
  
public class Company {  
    public static void main(String[] args) {  
        Employee emp = new Developer();  
        emp.work();  
    }  
}

Output

Writing code.

## Sealed Classes with Pattern Matching (Java 17+)

Sealed classes work seamlessly with modern switch expressions, enabling exhaustive checks.

sealed interface Shape permits Circle, Square {}  
  
record Circle(double radius) implements Shape {}  
record Square(double side) implements Shape {}  
  
public class ShapeTest {  
    public static void main(String[] args) {  
        Shape shape = new Circle(5);  
  
        String result = switch (shape) {  
            case Circle c -> "Circle with radius " + c.radius();  
            case Square s -> "Square with side " + s.side();  
        };  
  
        System.out.println(result);  
    }  
}

Output

Circle with radius 5.0

_Note:_ Pattern matching for switch became a standard feature in Java 21.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:875/1*7mBZgufI-JGIi4xFzxkHAA.png)



## Common Mistakes to Avoid

- Forgetting to specify the **permits** clause.
- Not declaring subclasses as **final**, **sealed**, or **non-sealed**.
- Attempting to extend a sealed class without permission.
- Placing permitted subclasses in different modules without proper configuration.
- Using sealed classes in Java versions earlier than Java 17.
- Misunderstanding the difference between final and sealed.

## Best Practices

- Use sealed classes to model **fixed and controlled hierarchies**.
- Prefer sealed types in **domain-driven design (DDD)**.
- Combine sealed classes with **records** for immutable data models.
- Use them with **pattern matching** for cleaner and safer code.
- Keep permitted subclasses logically related.
- Document your sealed hierarchies for maintainability.

---
# Java Collections tricks


In this article, we’ll explore **10 advanced Java Collections tricks that every senior developer should know** — explained clearly with real-world examples so students and professionals alike can benefit.

1. Use Collections.emptyList(), emptySet(), emptyMap() Instead of Creating New Empty Collections

Instead of creating unnecessary empty collections like this:

List<String> list = new ArrayList<>();

**Prefer using:**

List<String> list = Collections.emptyList();

### Why this is better:

- **Saves memory** — uses a shared singleton instance.
- **Immutable** — prevents accidental modification.
- **Cleaner and more expressive** — clearly conveys intent.

This approach is ideal when you want to return an empty collection safely and efficiently.

### 2. Prefer List.of(), Set.of(), and Map.of() for Immutable Collections (Java 9+)

Java 9 introduced convenient factory methods for creating small immutable collections:

List<String> fruits = List.of("Apple", "Banana", "Mango");  
Set<Integer> ids = Set.of(1, 2, 3);  
Map<Integer, String> map = Map.of(1, "A", 2, "B");

**Benefits:**

- **Concise and readable syntax**
- **Immutable by default** — prevents unintended changes
- **More efficient and cleaner** than wrapping collections with  
    Collections.unmodifiableList()

Perfect for fixed configuration values, constants, and read-only datasets.

### 3. Use Collections.unmodifiableList() to Build Safe APIs

When exposing internal collections from APIs, never return mutable collections directly:

public List<String> getItems() {  
    return Collections.unmodifiableList(items);  
}

### Why:

- Protects your internal data from modification
- Improves **encapsulation**
- Prevents unexpected bugs caused by external mutation

This ensures your API remains **safe, predictable, and robust**.

### 4. Convert Between Collections in One Line

Instead of writing manual loops:

for (String s : list) {  
    set.add(s);  
}

You can convert collections cleanly using constructors:

Set<String> set = new HashSet<>(list);  
List<String> list = new ArrayList<>(set);

### Advantages:

- Cleaner and shorter code
- Improved readability
- Less boilerplate and fewer chances of errors

Senior developers rely on this approach for **quick and elegant conversions**.

### 5. Sort Collections Using Collections.sort() or List.sort() with Lambda

**Basic Sorting:**

Collections.sort(list);

**Custom Sorting Using Lambda (Java 8+):**

list.sort(Comparator.comparing(String::length));

### Why this is better:

- Clean and readable syntax
- Flexible custom sorting logic
- Less boilerplate code

Lambda-based sorting makes your intent clear and your code concise.

### 6. Use computeIfAbsent() in Maps to Avoid Manual Null Checks

**Old Approach:**

if (!map.containsKey(key)) {  
    map.put(key, new ArrayList<>());  
}  
map.get(key).add(value);

**Better Approach:**

map.computeIfAbsent(key, k -> new ArrayList<>()).add(value);

### Benefits:

- Cleaner and more readable
- Avoids unnecessary map lookups
- Prevents **NullPointerException**

This is a **must-know trick** for building grouped data structures.

### 7. Use Collections.frequency() to Count Occurrences

**Traditional Loop:**

int count = 0;  
for (String s : list) {  
    if (s.equals("apple")) count++;  
}

**Cleaner Way:**

int count = Collections.frequency(list, "apple");

### 8. Use Collections.disjoint() to Check for Non-Overlapping Collections

Instead of writing nested loops:

boolean noCommon = Collections.disjoint(list1, list2);

### What it does:

- Returns **true** if both collections have no elements in common
- Fast and very expressive

Perfect for validation checks and filtering logic.

### 9. Shuffle Collections Randomly

Need to randomize the order of elements?

Collections.shuffle(list);

### Common use cases:

- Games
- Simulations
- Randomized test cases
- Load balancing

### 10. Use Map.merge() to Simplify Frequency Counters

**Traditional Way:**

if (map.containsKey(word)) {  
    map.put(word, map.get(word) + 1);  
} else {  
    map.put(word, 1);  
}

**Elegant One-Liner:**

map.merge(word, 1, Integer::sum);

### Why senior devs love this:

- Ultra-clean syntax
- No conditionals needed
- Perfect for word count, log analysis, and metrics



 # @JsonIgnore vs @Transient

**We use the @JsonIgnore annotation to specify a method or field that should be ignored during serialization and deserialization processes.** This marker annotation belongs to the [Jackson](https://www.baeldung.com/jackson) library.

We often apply this annotation to exclude fields that may not be relevant or could contain sensitive information. We use it on a field or a method to mark a property we’d like to ignore.


On the other hand, we use the @Transient annotation to indicate the [Java Persistence API] (JPA) should ignore the field when mapping objects to a database. **When we mark a field with this annotation, the JPA won’t persist the field and it won’t retrieve its value from the database.**



---

# Reflection API

The Java Reflection API allows a program to inspect and manipulate its own internal structure, such as classes, methods, fields, and constructors, at runtime. It is primarily provided through the package and the class. 

Core Components

To perform reflection, you must first obtain a object, which serves as the entry point.

- java.lang.Class: Represents the metadata of a class or interface.
- java.lang.reflect.Field: Provides information about and dynamic access to a single field of a class.
- java.lang.reflect.Method: Used to discover and invoke methods dynamically.
- java.lang.reflect.Constructor: Enables the creation of new objects at runtime without using the keyword. 

Common Use Cases

Reflection is essential for building flexible and dynamic systems.

- Framework Development: Modern frameworks like Spring use reflection for Dependency Injection and bean creation, while JUnit uses it to find and execute test cases marked with .
- Bypassing Access Rules: Reflection can access and modify fields and methods by calling .
- Dynamic Proxies: The
    
    Proxy
    
      
    class uses reflection to create objects that implement interfaces at runtime.
- Serialization/Deserialization: Libraries like Jackson use reflection to map Java objects to JSON or XML

How to Use Reflection

1. Get the Class Object: Use , , or .
2. Access Members: Call methods like , , or on the class object.
3. Manipulate/Invoke: Use , , or to interact with the code. 



Drawbacks and Limitations

While powerful, reflection should be used sparingly due to several risks.

- Performance Overhead: Dynamic resolution is slower than direct calls because the JVM cannot perform certain optimizations.
- Security Risks: It breaks encapsulation by exposing internal private members, which can lead to vulnerabilities.
- Maintenance Difficulty: Since reflection relies on string-based names, code can break during refactoring without compile-time warnings. 

  

---

## Stream Gatherer's

# Java Stream Gatherer (Java 22+)

The `Stream.Gatherer` interface, introduced in Java 22 as a preview feature (finalized in Java 23), provides a powerful way to perform custom intermediate operations on streams. It's an alternative to `collect()` but operates on element-by-element basis.

## What is a Gatherer?

A `Gatherer` represents a transformation of stream elements, combining concepts from:
- **`flatMap`** - one-to-many transformation
- **`map`** - one-to-one transformation  
- **`filter`** - conditional inclusion
- **`reduce`** - stateful accumulation

## Core Methods of Gatherer

### 1. **`initializer()`** - Creates initial state
### 2. **`integrator()`** - Processes each element (mandatory)
### 3. **`combiner()`** - Merges states for parallel streams
### 4. **`finisher()`** - Final transformation after all elements

## Built-in Gatherers (Java 23+)

### Example 1: `fold()` - Stateful Reduction

```java
import java.util.stream.Gatherers;
import java.util.stream.Stream;

public class FoldExample {
    public static void main(String[] args) {
        // Running sum using fold
        var result = Stream.of(1, 2, 3, 4, 5)
            .gather(Gatherers.fold(() -> 0, (sum, n) -> sum + n))
            .findFirst()
            .orElse(0);
        
        System.out.println("Sum: " + result); // Sum: 15
        
        // Running product
        var product = Stream.of(2, 3, 4)
            .gather(Gatherers.fold(() -> 1, (acc, n) -> acc * n))
            .findFirst()
            .orElse(1);
        
        System.out.println("Product: " + product); // Product: 24
    }
}
```

### Example 2: `windowSliding()` - Fixed-size Windows

```java
public class WindowExample {
    public static void main(String[] args) {
        // Sliding window of size 3
        var windows = Stream.of(1, 2, 3, 4, 5, 6)
            .gather(Gatherers.windowSliding(3))
            .toList();
        
        windows.forEach(System.out::println);
        // Output:
        // [1, 2, 3]
        // [2, 3, 4]
        // [3, 4, 5]
        // [4, 5, 6]
        
        // Calculate moving average
        var movingAvg = Stream.of(10, 20, 30, 40, 50)
            .gather(Gatherers.windowSliding(3))
            .map(window -> window.stream().mapToInt(Integer::intValue).average().orElse(0))
            .toList();
        
        System.out.println("Moving averages: " + movingAvg);
        // Moving averages: [20.0, 30.0, 40.0]
    }
}
```

### Example 3: `windowFixed()` - Non-overlapping Windows

```java
public class WindowFixedExample {
    public static void main(String[] args) {
        // Fixed windows of size 3
        var batches = Stream.of(1, 2, 3, 4, 5, 6, 7, 8)
            .gather(Gatherers.windowFixed(3))
            .toList();
        
        batches.forEach(System.out::println);
        // Output:
        // [1, 2, 3]
        // [4, 5, 6]
        // [7, 8]  (last window may be smaller)
        
        // Process in batches
        var batchSum = Stream.of(1, 2, 3, 4, 5, 6, 7, 8)
            .gather(Gatherers.windowFixed(4))
            .map(batch -> batch.stream().mapToInt(Integer::intValue).sum())
            .toList();
        
        System.out.println("Batch sums: " + batchSum); // Batch sums: [10, 26]
    }
}
```

### Example 4: `scan()` - Cumulative Transformations

```java
public class ScanExample {
    public static void main(String[] args) {
        // Running sum (cumulative)
        var runningSum = Stream.of(1, 2, 3, 4, 5)
            .gather(Gatherers.scan(() -> 0, (sum, n) -> sum + n))
            .toList();
        
        System.out.println("Running sum: " + runningSum);
        // Running sum: [1, 3, 6, 10, 15]
        
        // Running maximum
        var runningMax = Stream.of(3, 1, 4, 1, 5, 9, 2)
            .gather(Gatherers.scan(() -> Integer.MIN_VALUE, 
                (max, n) -> Math.max(max, n)))
            .toList();
        
        System.out.println("Running max: " + runningMax);
        // Running max: [3, 3, 4, 4, 5, 9, 9]
    }
}
```

## Custom Gatherer Example

```java
import java.util.*;
import java.util.function.BiConsumer;
import java.util.function.Supplier;
import java.util.stream.Gatherer;

public class CustomGathererExample {
    
    // Custom gatherer that groups consecutive equal elements
    public static <T> Gatherer<T, List<T>, List<T>> groupConsecutive() {
        return Gatherer.ofSequential(
            // Initializer: empty list
            (Supplier<List<T>>) ArrayList::new,
            
            // Integrator: process each element
            Gatherer.Integrator.ofGreedy((state, element, downstream) -> {
                if (state.isEmpty() || state.get(0).equals(element)) {
                    state.add(element);
                    return true;
                } else {
                    // Send current group downstream
                    downstream.push(new ArrayList<>(state));
                    state.clear();
                    state.add(element);
                    return true;
                }
            }),
            
            // Finisher: send last group
            (state, downstream) -> {
                if (!state.isEmpty()) {
                    downstream.push(new ArrayList<>(state));
                }
            }
        );
    }
    
    public static void main(String[] args) {
        var result = Stream.of(1, 1, 2, 2, 2, 3, 1, 1, 4)
            .gather(groupConsecutive())
            .toList();
        
        result.forEach(System.out::println);
        // Output:
        // [1, 1]
        // [2, 2, 2]
        // [3]
        // [1, 1]
        // [4]
    }
}
```

## Advanced Custom Gatherer: Distinct by Key

```java
public class DistinctByKeyExample {
    
    public static <T, K> Gatherer<T, Map<K, T>, T> distinctByKey(
            java.util.function.Function<? super T, ? extends K> keyExtractor) {
        
        return Gatherer.of(
            // Initializer
            HashMap::new,
            
            // Integrator
            Gatherer.Integrator.ofGreedy((state, element, downstream) -> {
                K key = keyExtractor.apply(element);
                if (!state.containsKey(key)) {
                    state.put(key, element);
                    downstream.push(element);
                }
                return true;
            }),
            
            // Combiner for parallel streams
            (left, right) -> {
                left.putAll(right);
                return left;
            },
            
            // Finisher (no-op)
            (state, downstream) -> {}
        );
    }
    
    public static void main(String[] args) {
        record Person(String name, int age) {}
        
        var people = Stream.of(
            new Person("Alice", 30),
            new Person("Bob", 25),
            new Person("Alice", 35),  // Duplicate name
            new Person("Charlie", 30), // Duplicate age
            new Person("David", 25)
        );
        
        // Distinct by name
        var uniqueByName = people
            .gather(distinctByKey(Person::name))
            .toList();
        
        uniqueByName.forEach(System.out::println);
        // Output:
        // Person[name=Alice, age=30]
        // Person[name=Bob, age=25]
        // Person[name=Charlie, age=30]
        // Person[name=David, age=25]
        // (Alice with age 35 is filtered out)
    }
}
```

## Gatherer vs Collector Comparison

```java
public class ComparisonExample {
    public static void main(String[] args) {
        // Collector: terminal operation, produces single result
        var sum = Stream.of(1, 2, 3, 4, 5)
            .collect(Collectors.summingInt(Integer::intValue));
        // Result: 15
        
        // Gatherer: intermediate operation, produces stream
        var runningSum = Stream.of(1, 2, 3, 4, 5)
            .gather(Gatherers.scan(() -> 0, (s, n) -> s + n))
            .collect(Collectors.toList());
        // Result: [1, 3, 6, 10, 15]
    }
}
```

## Key Differences from Other Operations

| Operation | Purpose |
|-----------|---------|
| **`map`** | One-to-one, stateless |
| **`flatMap`** | One-to-many, stateless |
| **`filter`** | Selective inclusion, stateless |
| **`reduce`** | Terminal, single result |
| **`collect`** | Terminal, mutable result |
| **`gather`** | **Intermediate, stateful, one-to-many** |

## Performance Considerations

```java
public class PerformanceExample {
    public static void main(String[] args) {
        // Prefer built-in gatherers when possible
        var efficient = Stream.generate(Math::random)
            .limit(1_000_000)
            .gather(Gatherers.windowSliding(100))
            .limit(10)  // Short-circuiting works!
            .toList();
        
        // Custom gatherers can be optimized for parallel processing
        var parallel = Stream.of(1, 2, 3, 4, 5, 6, 7, 8)
            .parallel()
            .gather(new ParallelFriendlyGatherer<>())
            .toList();
    }
}
```

## Summary

**Gatherers** excel at:
- Stateful transformations
- Element grouping and windowing
- Cumulative operations
- Both one-to-one and one-to-many mappings
- Parallel execution support

They fill the gap between simple stateless operations (`map`, `filter`) and terminal operations (`collect`, `reduce`), providing a flexible intermediate operation pattern.

---------

