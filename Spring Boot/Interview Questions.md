

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


---
## Question 1: Why Does `@Transactional` Sometimes Not Work?

@Service  
public class PaymentService {

    public void process() {  
        savePayment(); // Is this transactional?  
    }    @Transactional  
    public void savePayment() {  
        // database operations  
    }  
}

**What most people say:** “It should work — `savePayment()` is annotated."

**What’s actually happening:** No transaction. And no error to tell you that.

Spring’s transaction management works through proxy objects. When you inject `PaymentService` into another class and call `process()`, you're calling it through a Spring-generated proxy that intercepts the call. But when `process()` calls `this.savePayment()`, it's calling directly on the underlying object — bypassing the proxy entirely. No interception. No transaction.

The `@Transactional` annotation is present. The transaction is not.

This is one of the most common causes of silent data integrity bugs in Spring applications. The method executes, no exception is thrown, but if something fails mid-operation, nothing rolls back.

**The fix:** restructure so the transactional method is called through the proxy (inject the bean into itself, or refactor the method into a separate service), or use `AopContext.currentProxy()` — though the cleaner answer is always the redesign.

**The deeper question a senior interviewer asks next:** “What other annotations have the same proxy limitation?” — `@Async` and `@Cacheable` are the answers. All Spring AOP-based annotations fail silently on self-invocation.

## Question 2: What Happens When `@Async` Is Called From the Same Class?

@Service  
public class NotificationService {

    public void sendAll() {  
        sendEmail(); // Is this asynchronous?  
    }    @Async  
    public void sendEmail() {  
        // supposed to run in a different thread  
    }  
}

**What most people say:** “It runs asynchronously — that’s what `@Async` does."

**What’s actually happening:** It runs synchronously. On the same thread. In the same call stack. And Spring gives you no warning.

Same proxy problem as `@Transactional`. The `@Async` annotation only takes effect when the method is called through the Spring proxy — which happens when another bean calls it. Internal calls bypass the proxy, so `@Async` is silently ignored.

The dangerous part: the code appears to work in development and testing. Both paths execute. No exception. But under production load, the “async” tasks are blocking the calling thread — which can saturate your thread pool and introduce latency you can’t explain from the logs.

**The fix:** same as `@Transactional` — call through the proxy, not `this`. Which means the async method typically belongs in a separate component.

This question reveals whether a candidate understands Spring AOP as a concept or just knows a list of annotations.

## Question 3: Why Does Spring Boot Fail on Circular Dependencies?

@Service  
public class ServiceA {  
    @Autowired  
    private ServiceB serviceB;  
}

@Service  
public class ServiceB {  
    @Autowired  
    private ServiceA serviceA;  
}

**What most people say:** “Circular dependency bad. Use `@Lazy` to fix it."

**What’s actually happening:** Spring builds beans during container initialization. When it tries to create `ServiceA`, it needs `ServiceB`. When it tries to create `ServiceB`, it needs `ServiceA`. Neither can be fully constructed until the other exists.

For constructor injection, this is a hard deadlock — Spring throws `BeanCurrentlyInCreationException` immediately. For field injection, Spring can sometimes resolve it using partial bean references (which is exactly why field injection is dangerous — the problem may be masked).

**The deeper insight:** circular dependencies usually indicate a design problem, not a configuration problem. Two beans that need each other belong in one bean, or there’s a third concept that should be extracted. `@Lazy` breaks the cycle but doesn't fix the design.

**Most candidates stop at “circular dependency bad.” Strong candidates explain bean lifecycle, initialization order, and why the design needs rethinking.**

## Question 4: What Actually Makes Spring Boot Fast to Develop?

**What most people say:** “Starters and auto-configuration.”

**What’s actually happening:** Starters are just dependency bundles — they don’t configure anything themselves. The mechanism behind “fast” is auto-configuration, and understanding how it works is what separates engineers who use Spring Boot from engineers who understand it.

Auto-configuration classes are loaded via `spring.factories` (or `AutoConfiguration.imports` in newer versions). Each one uses `@Conditional` annotations to decide whether to activate:

@Configuration  
@ConditionalOnClass(DataSource.class)  
@ConditionalOnMissingBean(DataSource.class)  
public class DataSourceAutoConfiguration {  
    // configures datasource only if one isn't already defined  
}

Add `spring-boot-starter-data-jpa` and Spring Boot automatically configures a `DataSource`, `EntityManagerFactory`, `TransactionManager`, and repositories — but only if you haven't already defined your own. Your custom bean takes precedence.

**Spring Boot is not magic. It’s convention-driven automation with intelligent fallbacks.**

The follow-up that trips most engineers: “How would you debug why an auto-configuration isn’t applying?” — the answer is `--debug` flag or `ConditionEvaluationReport`, which shows every conditional evaluation and why it passed or failed.

## Question 5: Are `@Component`, `@Service`, and `@Repository` Actually Different?

**What most people say:** “They’re all the same — just semantic conventions for readability.”

**What’s actually happening:** `@Component` and `@Service` are functionally identical. But `@Repository` does something different.

Spring adds automatic exception translation to `@Repository` beans. If a persistence layer throws a driver-specific exception — a `SQLException`, a `HibernateException`, a JPA-specific error — Spring wraps it in its `DataAccessException` hierarchy. Your service layer receives a portable, Spring-managed exception instead of a vendor-specific one.

@Repository  
public class UserRepository {  
    // SQLException here becomes DataAccessException  
    // HibernateException here becomes DataAccessException  
}

Without `@Repository`, these exceptions propagate as-is. Your service layer becomes coupled to the persistence technology — if you switch from Hibernate to JDBC, the exceptions change.

**The annotation is not just semantic. It changes behavior.**

The deeper question: “Why does this matter for production systems?” — because exception handling strategy and the ability to swap persistence implementations without changing service logic are real architectural concerns.

## The Pattern Across All These Questions

Every question above tests the same thing: do you understand the framework as a set of contracts and mechanisms, or as a set of annotations to apply?

The proxy model underlies `@Transactional`, `@Async`, and `@Cacheable`. Bean lifecycle underlies circular dependencies, `ApplicationContext` behavior, and auto-configuration. Thread model underlies WebFlux vs MVC.

**Production systems fail underneath abstractions. Senior engineers are expected to understand what’s underneath.**

This is usually the difference between a mid-level and a senior-level interview performance. Not the ability to build features — the ability to reason about why the framework behaves the way it does when something goes wrong.

---
