

The **Spring Boot application lifecycle** refers to the sequence of phases a Spring Boot application goes through from startup to shutdown. Understanding this lifecycle helps in configuring beans, managing resources, and handling application events effectively.

---

## **Spring Boot Application Lifecycle Phases**
### **1. Application Startup**
When a Spring Boot app starts (`main()` method execution), the following happens:

#### **A. Bootstrap Phase**
- **`SpringApplication`** class initializes the application context.
- **Spring Factories Loader** reads `META-INF/spring.factories` to load auto-configurations.
- **Environment Setup** (properties, profiles, etc.) is prepared.

#### **B. Context Initialization**
- **`ApplicationContext`** (usually `AnnotationConfigApplicationContext` or `SpringApplicationContext`) is created.
- **Bean Definitions** are loaded (`@Component`, `@Service`, `@Repository`, `@Controller`, etc.).
- **Auto-configuration** (`@EnableAutoConfiguration`) processes conditional beans.

#### **C. Bean Instantiation & Dependency Injection**
- **Beans** are instantiated (via constructors or factory methods).
- **Dependencies** are injected (`@Autowired`, `@Resource`).
- **Post-Processors** (e.g., `BeanPostProcessor`) modify beans before/after initialization.

#### **D. Application Events (During Startup)**
- **`ApplicationStartingEvent`** → Fired at the very beginning.
- **`ApplicationEnvironmentPreparedEvent`** → After environment is ready.
- **`ApplicationContextInitializedEvent`** → Before beans are loaded.
- **`ApplicationPreparedEvent`** → After beans are loaded but before refresh.
- **`ApplicationStartedEvent`** → After context is refreshed.
- **`ApplicationReadyEvent`** → When the app is fully ready.

---

### **2. Application Running**
- The app is **fully initialized** and **accepting requests** (if it's a web app).
- **Scheduled tasks** (`@Scheduled`) start executing.
- **Actuator endpoints** (if enabled) become available for monitoring.

---

### **3. Application Shutdown**
When the app shuts down (e.g., via `Ctrl+C`, `kill`, or `SpringApplication.exit()`):

#### **A. Graceful Shutdown (If Enabled)**
- **`ContextClosedEvent`** is triggered.
- **`@PreDestroy`** methods are called (for cleanup logic).
- **Database connections, thread pools, etc.** are closed gracefully.

#### **B. Force Shutdown (Unexpected Crash)**
- If the JVM is killed abruptly, shutdown hooks may not execute.
- Best practice: Use **`SpringApplication.setRegisterShutdownHook(true)`** (default).

---

## **Key Lifecycle Hooks**
You can tap into the lifecycle using:

1. **`@PostConstruct` & `@PreDestroy`** (JSR-250)  
   ```java
   @Service
   public class MyService {
       @PostConstruct
       public void init() {
           System.out.println("Bean initialized!");
       }

       @PreDestroy
       public void cleanup() {
           System.out.println("Bean destroyed!");
       }
   }
   ```

2. **`ApplicationListener` (for Spring Events)**  
   ```java
   @Component
   public class MyListener implements ApplicationListener<ApplicationReadyEvent> {
       @Override
       public void onApplicationEvent(ApplicationReadyEvent event) {
           System.out.println("App is ready!");
       }
   }
   ```

3. **`CommandLineRunner` & `ApplicationRunner`**  
   ```java
   @Component
   public class MyRunner implements CommandLineRunner {
       @Override
       public void run(String... args) {
           System.out.println("Running after startup!");
       }
   }
   ```

4. **`SmartLifecycle` (for custom phased startup/shutdown)**  
   ```java
   @Component
   public class MyLifecycle implements SmartLifecycle {
       @Override
       public void start() { /* Custom startup */ }
       @Override
       public void stop() { /* Custom shutdown */ }
   }
   ```

---

## **Summary of Spring Boot Lifecycle**
| Phase | Key Actions |
|--------|------------|
| **Startup** | Load config → Create context → Initialize beans → Trigger events |
| **Running** | Serve requests, run scheduled tasks, expose actuator endpoints |
| **Shutdown** | Trigger `@PreDestroy`, close resources, fire `ContextClosedEvent` |

---

### **Best Practices**
✔ Use **`@PostConstruct`** for initialization logic.  
✔ Use **`@PreDestroy`** for cleanup (e.g., closing DB connections).  
✔ Listen to **`ApplicationReadyEvent`** (not `ContextRefreshedEvent`) for safe startup actions.  
✔ For web apps, enable **graceful shutdown** (`server.shutdown=graceful`).  

Would you like a **diagram** or a **debugging tip** for lifecycle issues? 😊