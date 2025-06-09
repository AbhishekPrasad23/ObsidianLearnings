

# [Garbage Collection in Java | What is GC and How does it Work in the JVM? | Types of GCs | Geekific](https://www.youtube.com/watch?v=XXOaCV5xm9s)

Garbage Collection (GC) is Java's automatic memory management system that frees developers from manually allocating and deallocating memory. It's one of Java's key features that helps prevent memory leaks and other memory-related issues.

## How Garbage Collection Works

### 1. Memory Allocation
- When objects are created in Java (using the `new` keyword), they're allocated memory in the **heap**.
- The heap is divided into generations to optimize garbage collection.

### 2. Generational Heap Structure
Java's heap is divided into three main generations:

1. **Young Generation**
   - Where new objects are allocated
   - Divided into:
     - **Eden space**: Where all new objects start
     - **Survivor spaces** (S0 and S1): Where objects that survive GC are moved

2. **Old Generation (Tenured)**
   - Contains long-lived objects that have survived multiple GC cycles in the Young Generation

3. **Permanent Generation (or Metaspace in Java 8+)**
   - Stores metadata like class definitions (removed in Java 8, replaced by Metaspace)

### 3. Garbage Collection Process

#### Minor GC (Young Generation Collection)
1. All new objects are created in Eden space.
2. When Eden fills up, a **minor GC** is triggered:
   - Live objects are copied from Eden to one survivor space (S0).
   - Dead objects are reclaimed.
3. On subsequent minor GCs:
   - Live objects from Eden and the occupied survivor space (S0) are copied to the other survivor space (S1).
   - Objects that have survived enough cycles (default threshold is 15) are promoted to the Old Generation.

#### Major GC (Old Generation Collection)
- Triggered when the Old Generation is full.
- More expensive than minor GC as it involves all live objects.
- Different algorithms can be used (see below).

### 4. GC Algorithms

Java provides several garbage collection algorithms:

1. **Serial GC**
   - Single-threaded, uses mark-compact for Old Generation
   - Good for small applications with low memory footprint

2. **Parallel GC (Throughput Collector)**
   - Default in Java 8
   - Uses multiple threads for Young Generation collection
   - Still stops application threads (Stop-The-World)

3. **CMS (Concurrent Mark-Sweep)**
   - Aims to minimize pauses by doing most work concurrently
   - Uses multiple threads for Old Generation collection
   - Deprecated in Java 9, removed in Java 14

4. **G1 GC (Garbage-First)**
   - Default since Java 9
   - Divides heap into equal-sized regions
   - Prioritizes collection of regions with most garbage
   - Designed for large heaps with low pause time goals

5. **ZGC (Z Garbage Collector)**
   - Scalable low-latency collector
   - Pause times < 10ms even for very large heaps
   - Added in Java 11 as experimental, production-ready in Java 15

6. **Shenandoah**
   - Similar to ZGC but developed by Red Hat
   - Focuses on consistent low pause times
   - Added in Java 12

### 5. Finalization

Before an object is garbage collected:
1. The GC calls the object's `finalize()` method (if overridden).
2. This gives the object a last chance to release resources.
3. However, relying on `finalize()` is discouraged because:
   - It's not guaranteed to run promptly
   - It can impact performance
   - It might resurrect the object (though this is bad practice)

## Key Concepts

### Reachability
An object is eligible for GC if it's **unreachable**:
- No live thread has a reference to it
- No static or other long-lived references point to it

### GC Roots
Objects that are always reachable include:
- Local variables and parameters in stack frames
- Active Java threads
- Static variables
- JNI references

### Memory Leaks in Java
Even with GC, memory leaks can occur when:
- Objects are unintentionally retained (e.g., in static collections)
- Listeners/callbacks aren't properly removed
- Resources aren't closed (use try-with-resources)

## Best Practices

1. **Minimize object creation** where possible (but don't over-optimize prematurely)
2. **Nullify references** explicitly when done with large objects
3. **Avoid finalize()** - use `AutoCloseable` and try-with-resources instead
4. **Choose the right GC** based on your application's needs
5. **Monitor GC activity** using JVM flags and tools like VisualVM, JConsole

## Monitoring and Tuning

Common JVM flags for GC:
- `-Xms` / `-Xmx`: Set initial and maximum heap size
- `-XX:+UseG1GC`: Use G1 garbage collector
- `-XX:+PrintGCDetails`: Print GC details
- `-XX:MaxGCPauseMillis=200`: Set target max GC pause time (for G1)

Example of analyzing GC logs can help identify:
- Frequency of collections
- Pause times
- Memory promotion rates

Garbage collection is a complex but crucial part of Java's memory management that allows developers to focus on business logic rather than manual memory management.




# Understanding `System.gc()` in Java

`System.gc()` is a method that suggests the Java Virtual Machine (JVM) to perform garbage collection. However, it's important to understand its behavior and implications.

## How `System.gc()` Works

1. **Suggestion, Not Command**
   - Calling `System.gc()` is merely a suggestion to the JVM
   - The JVM may or may not honor this request
   - The garbage collector ultimately decides when to run

2. **Triggers a Full GC**
   - When honored, it typically triggers a major garbage collection (full GC)
   - This collects both young and old generations
   - More expensive than regular minor collections

3. **Equivalent to Runtime.getRuntime().gc()**
   - `System.gc()` is just a convenience method that calls `Runtime.getRuntime().gc()`

## Why It's Generally Discouraged

1. **Performance Impact**
   - Full GC stops all application threads (Stop-The-World)
   - Can cause noticeable pauses in your application
   - May collect more than necessary

2. **JVM Knows Better**
   - Modern JVMs have sophisticated GC algorithms
   - They're optimized to run when most efficient
   - Manual intervention often reduces performance

3. **Unpredictable Behavior**
   - Different JVMs may respond differently
   - Behavior varies between GC algorithms (G1, CMS, ZGC, etc.)
   - Some JVMs may ignore it completely

## Proper Usage (Rare Cases)

There are very few legitimate use cases:

1. **Before Measuring Performance**
   - Might call before benchmarks to get clean measurements
   - But better alternatives usually exist

2. **Memory-Sensitive Operations**
   - Before a known large memory allocation
   - But proper memory management is better

3. **Debugging**
   - Forcing GC to check for memory leaks
   - Better to use proper profiling tools

## Better Alternatives

1. **Let the JVM Manage GC**
   - Trust the automatic memory management
   - Tune GC parameters if needed

2. **Use Memory-Sensitive Coding**
   - Limit object creation in critical paths
   - Reuse objects where appropriate
   - Properly close resources

3. **For Testing/Profiling**
   ```java
   // Better than System.gc() for testing
   Runtime.getRuntime().runFinalization();
   Runtime.getRuntime().gc();
   ```

## JVM Options Related to `System.gc()`

1. **Disabling Explicit GC**
   - `-XX:+DisableExplicitGC` - Makes `System.gc()` a no-op
   - Often used in production to prevent accidental calls

2. **Forcing Full GC**
   - `-XX:+ExplicitGCInvokesConcurrent`
   - Makes explicit GC use concurrent collection (if supported)

## Example (What Not to Do)

```java
// Bad practice - don't do this in production code
void processData() {
    // Process data
    System.gc();  // Unnecessary and potentially harmful
    // More processing
}
```

## When You Might See It Used

1. **Legacy Code**
   - Older code sometimes used it as a "fix" for memory issues

2. **Educational Examples**
   - Sometimes used in tutorials to demonstrate GC concepts

3. **Native Code Integration**
   - Rare cases when interfacing with native libraries

## Best Practice

The overwhelming consensus in the Java community is:
- **Don't use `System.gc()` in production code**
- Let the JVM handle garbage collection automatically
- If you have memory issues, profile properly and:
  - Fix memory leaks
  - Tune GC parameters
  - Optimize memory usage

Remember that Java's garbage collection is highly optimized, and manual intervention typically does more harm than good.