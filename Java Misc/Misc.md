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