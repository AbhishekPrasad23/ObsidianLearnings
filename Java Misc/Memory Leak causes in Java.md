

# Most Common Causes of Memory Leaks in Java

Even with Java's garbage collection, memory leaks can still occur when objects are no longer needed but remain referenced. Here are the most common causes:

## 1. **Static Collections/Fields**
   - Objects added to static collections persist for the JVM's lifetime
   - Example:
     ```java
     public class Cache {
         private static final Map<String, Object> CACHE = new HashMap<>();
         
         public void addToCache(String key, Object value) {
             CACHE.put(key, value); // Will never be GC'd unless explicitly removed
         }
     }
     ```

## 2. **Unclosed Resources**
   - File streams, database connections, sockets not properly closed
   - Example:
     ```java
     public void readFile() {
         FileInputStream fis = new FileInputStream("largefile.txt");
         // Forgot fis.close() - retains memory until GC runs
     }
     ```
   - **Solution:** Use try-with-resources:
     ```java
     try (FileInputStream fis = new FileInputStream("largefile.txt")) {
         // auto-closed
     }
     ```

## 3. **Listener/Callback Registrations**
   - Failing to remove listeners when no longer needed
   - Example:
     ```java
     button.addActionListener(new ActionListener() {
         public void actionPerformed(ActionEvent e) {
             // handler code
         }
     });
     // Never removed - keeps reference to button and context
     ```

## 4. **ThreadLocal Variables**
   - Values persist until the thread dies (problematic in thread pools)
   - Example:
     ```java
     private static final ThreadLocal<ExpensiveObject> threadLocal = 
         ThreadLocal.withInitial(ExpensiveObject::new);
     // If not removed, stays in memory for pooled threads
     ```
   - **Solution:** Always call `threadLocal.remove()` when done

## 5. **Classloader Issues**
   - Common in application servers when redeploying applications
   - Example: Holding references to classes from previous deployments

## 6. **String Intern Pool**
   - Indiscriminate use of `String.intern()` can fill up PermGen/Metaspace
   - Example:
     ```java
     while (true) {
         String unique = generateUniqueString();
         unique.intern(); // Adds to permanent string pool
     }
     ```

## 7. **Mismanaged Caches**
   - Caches without size limits or eviction policies
   - Example:
     ```java
     Map<Key, LargeObject> cache = new HashMap<>(); // Grows indefinitely
     ```
   - **Solution:** Use `WeakHashMap` or caching libraries (Guava, Caffeine) with eviction

## 8. **Non-Static Inner Classes**
   - Implicit reference to outer class prevents GC
   - Example:
     ```java
     public class Outer {
         private byte[] largeData;
         
         public Runnable createTask() {
             return new Runnable() { // Implicit reference to Outer.this
                 public void run() { /* uses largeData */ }
             };
         }
     }
     ```
   - **Solution:** Make static when possible

## Detection Tools
- **VisualVM** (Basic analysis)
- **Eclipse Memory Analyzer (MAT)** (Detailed heap dumps)
- **YourKit** (Commercial profiler)
- **JProfiler** (Commercial profiler)

## Prevention Best Practices
1. Use weak references (`WeakReference`, `WeakHashMap`) for caches
2. Always close resources in `finally` blocks or use try-with-resources
3. Clean up collections that are no longer needed
4. Remove listeners/callbacks when done
5. Be cautious with static collections - implement size limits
6. Profile memory usage regularly, especially after deployments