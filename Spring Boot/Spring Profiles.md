In Spring Boot, the `@Profile` annotation is used to conditionally enable or disable certain beans or configurations based on the active profiles. This is useful when you want different behaviors in different environments (e.g., `dev`, `test`, `prod`).

### **1. Basic Usage of `@Profile`**
You can apply `@Profile` to:
- **Bean methods** in `@Configuration` classes.
- **Entire `@Component`, `@Service`, `@Repository`, or `@Controller` classes.
- **`@Configuration` classes themselves.**

#### Example:
```java
@Configuration
public class AppConfig {

    @Bean
    @Profile("dev")
    public DataSource devDataSource() {
        return new EmbeddedDatabaseBuilder()
                .setType(EmbeddedDatabaseType.H2)
                .build();
    }

    @Bean
    @Profile("prod")
    public DataSource prodDataSource() {
        // Configure a production datasource (e.g., MySQL, PostgreSQL)
        return DataSourceBuilder.create().build();
    }
}
```
- If the `dev` profile is active, `devDataSource()` will be used.
- If the `prod` profile is active, `prodDataSource()` will be used.

---

### **2. Activating Profiles**
You can activate profiles in several ways:

#### **a) Using `application.properties` or `application.yml`**
```properties
# application.properties
spring.profiles.active=dev
```
```yaml
# application.yml
spring:
  profiles:
    active: dev
```

#### **b) Via Command Line (when running the JAR)**
```bash
java -jar myapp.jar --spring.profiles.active=dev,test
```

#### **c) Programmatically (e.g., in `main`)**
```java
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(MyApp.class);
    app.setAdditionalProfiles("dev");
    app.run(args);
}
```

#### **d) Using Environment Variables**
```bash
export SPRING_PROFILES_ACTIVE=prod
java -jar myapp.jar
```

---

### **3. Profile-Specific Properties Files**
Spring Boot allows you to define profile-specific properties files like:
- `application-dev.properties` (for `dev` profile)
- `application-prod.properties` (for `prod` profile)

These override the default `application.properties` when the corresponding profile is active.

---

### **4. Combining Profiles with `@Profile`**
You can use logical operators (`!`, `&`, `|`) in `@Profile`:
- `@Profile("dev")` → Only when `dev` is active.
- `@Profile("!prod")` → When `prod` is **not** active.
- `@Profile({"dev", "test"})` → When either `dev` or `test` is active.
- `@Profile("dev & !cloud")` → When `dev` is active **and** `cloud` is not.

---

### **5. Default Profile**
If no profile is active, Spring Boot uses the `default` profile. You can define `application-default.properties` for fallback configurations.

---

### **6. Checking Active Profiles**
You can check active profiles programmatically:
```java
@Autowired
private Environment env;

public void checkProfiles() {
    if (env.acceptsProfiles("dev")) {
        System.out.println("Running in DEV mode!");
    }
}
```
Or:
```java
@Autowired
private ApplicationContext ctx;

public void checkProfiles() {
    if (ctx.getEnvironment().acceptsProfiles("dev")) {
        System.out.println("DEV profile is active!");
    }
}
```

---

### **7. Profile Groups (Spring Boot 2.4+)**
You can group profiles together in `application.properties`:
```properties
spring.profiles.group.production=db,mq,security
spring.profiles.group.development=dev,h2
```
Now, activating `production` will enable `db`, `mq`, and `security`.

---

### **Summary**
| Feature | Example |
|---------|---------|
| Define profile-specific beans | `@Profile("dev")` |
| Activate profiles | `spring.profiles.active=dev` |
| Profile-specific properties | `application-dev.properties` |
| Logical conditions | `@Profile("dev & !cloud")` |
| Profile groups | `spring.profiles.group.production=db,mq` |

This helps in maintaining environment-specific configurations cleanly in Spring Boot. 🚀 Let me know if you need further clarification!