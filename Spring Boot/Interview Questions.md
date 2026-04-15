

---

### 🔍 Common Spring Boot Troubleshooting Scenarios

1. **Fast locally but slow in production**
    
    - First check: **environment differences** (network latency, DB location, JVM heap size, logging level, reverse proxy). Often production DB is remote, unlike local.
2. **BeanCreationException after deployment**
    
    - Likely cause: **missing configuration or dependency mismatch**. Could be profile-specific beans not loading, or a circular dependency introduced.
3. **DB connection pool exhausted under load**
    
    - Causes: **long-running queries**, improper pool size, connections not released, or transactions left open.
4. **@Transactional rollback not happening**
    
    - Common reason: rollback only triggers on **unchecked exceptions (RuntimeException)** by default. Checked exceptions won’t trigger unless explicitly configured.
5. **Low CPU but requests timing out**
    
    - Bottleneck is usually **I/O bound** (DB, external API, file system, network). CPU isn’t the limiting factor.
6. **application.properties changes not reflected**
    
    - Check if:
        - Config is overridden by **environment variables** or **externalized config**.
        - Old JAR still deployed.
        - Spring Cloud Config server caching.
7. **Scheduled jobs impacting API performance**
    
    - Jobs may be **competing for DB connections or threads**. If they run on the same pool, they starve API requests.
8. **Different behavior in Docker vs local**
    
    - Causes: **resource limits**, networking differences, volume mounts, or JVM inside container having different defaults.
9. **Logs missing in production**
    
    - Check logging config:
        - Logback/Log4j profile differences.
        - Logs redirected to stdout/stderr (containerized apps).
        - File permissions or log rotation.
10. **Adding more instances didn’t improve performance**
    
    - Likely bottleneck is **shared resource** (DB, external API). Scaling app instances doesn’t help if backend is the choke point.
11. **External API integration delays**
    
    - Debug with:
        - Timeout settings.
        - Circuit breaker (Resilience4j/Hystrix).
        - Network latency monitoring.
12. **Async processing worsened performance**
    
    - Possible reasons:
        - **Thread pool misconfigured** (too small or too large).
        - Async tasks blocking instead of non-blocking.
        - Increased context switching overhead.
13. **Users see old behavior after deployment**
    
    - Causes:
        - **Caching/CDN not invalidated**.
        - Old version still running in cluster.
        - Browser cache.
14. **Cache improved performance but stale data**
    
    - Root cause: **no proper eviction/invalidation strategy**. Cache needs TTL or explicit refresh triggers.
15. **Works fine with few users but fails under load**
    
    - Investigate:
        - **Thread pool exhaustion**.
        - DB connection pool limits.
        - Lock contention.
        - Memory leaks.

---

### ⚡ Key Takeaway

Most of these issues boil down to **environment differences, resource contention, and misconfiguration**. The trick is to isolate whether the bottleneck is **CPU, memory, I/O, or external dependency**, then trace it with profiling tools (JFR, Micrometer, Prometheus, etc.).

---

Would you like me to turn this into a **diagnostic checklist** (step-by-step flow you can follow when debugging), so you can apply it quickly in production incidents?



@Transactional will NOT work as expected with @Async in Spring Boot  
  
Many developers assume that adding "@Transactional" to an asynchronous method will automatically manage database transactions. But in reality, transactions often don’t behave as expected when used with async execution.  
  
🔍 Why does this happen?  
  
Spring manages transactions using proxy-based AOP. When we use "@Async", the method executes in a separate thread managed by Spring’s TaskExecutor. Because of this:  
  
- The transactional context from the main thread is NOT propagated to the async thread.  
- If "@Transactional" is used incorrectly, no transaction may be created at all.  
- Lazy loading issues and partial commits can occur.  
  
❌ Common mistake  
  
@Async  
@Transactional  
public void processData() {  
// database operations  
}  
  
Many assume this ensures transactional consistency, but the async proxy invocation can prevent proper transaction creation depending on how the method is called.  
  
✅ Correct approaches  
  
✔ Call async method from a different Spring bean (so proxy works)  
✔ Keep transaction boundary inside the async method  
✔ Avoid calling "@Async" method from the same class (self-invocation problem)  
✔ Use "Propagation.REQUIRES_NEW" if separate transaction is required  
  
@Async  
@Transactional(propagation = Propagation.REQUIRES_NEW)  
public void processDataAsync() {  
// executes in independent transaction  
}  
  
💡 Key takeaway  
  
"@Async" = different thread  
"@Transactional" = thread-bound  
  
If they are not structured properly, your transaction may silently fail.  
  
Understanding this behavior is very important when working with Spring Boot microservices, batch jobs, and background processing.