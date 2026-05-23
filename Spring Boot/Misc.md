

| 1   | [[#7 Ways to Make Your Spring Boot APIs 10x Faster]] |
| --- | ---------------------------------------------------- |
| 2   | [[#The Spring Boot Production Checklist]]            |
| 3   | [[#@ConfigurationProperties]]                        |
| 4   | [[#Designing Idempotent APIs in Spring Boot]]        |
| 5   | [[#ETags]]                                           |




# 7 Ways to Make Your Spring Boot APIs 10x Faster

Proven Performance Optimization Techniques to Reduce Latency, Increase Throughput, and Build High-Performance Spring Boot Services



## 1. Implement Smart Caching

One of the easiest ways to dramatically improve API performance is caching.

If your application repeatedly fetches the same data from the database, caching can eliminate unnecessary database queries.

Spring Boot provides built-in caching support.

Example:

@Cacheable("products")  
public Product getProductById(Long id) {  
    return productRepository.findById(id).orElse(null);  
}

Once cached, the data is returned directly from memory instead of hitting the database.

Popular caching systems include:

- Redis
- Memcached

Benefits of caching include:

- Faster API responses
- Reduced database load
- Better scalability

Caching is particularly effective for:

- Product catalogs
- Configuration data
- Frequently accessed records

However, always design proper cache invalidation strategies to prevent stale data.

## 2. Optimize Database Queries

In most applications, **database queries are the biggest performance bottleneck**.

A poorly optimized query can increase response times dramatically.

Common issues include:

- N+1 query problem
- Missing indexes
- Fetching unnecessary columns
- Full table scans

Example of inefficient query usage:

List<User> users = userRepository.findAll();

Instead, use projections or specific queries to fetch only required data.

Example:

@Query("SELECT u.name, u.email FROM User u")  
List<UserProjection> getUserSummary();

Also, ensure critical columns are indexed.

Databases like:

- PostgreSQL
- MySQL

provide powerful indexing capabilities.

Optimizing queries alone can sometimes reduce API latency by **70-80%**.

## 3. Use Asynchronous Processing

Many APIs perform tasks that don’t need to block the request.

Examples include:

- Sending emails
- Processing analytics data
- Logging heavy events
- Updating secondary systems

Instead of performing these tasks synchronously, run them asynchronously.

Spring Boot supports async execution using `@Async`.

Example:

@Async  
public void sendEmailNotification(Order order) {  
    emailService.send(order);  
}

This allows the API to return the response immediately while background processing continues.

For large-scale systems, integrate message brokers such as:

- Apache Kafka
- RabbitMQ

These tools enable distributed and highly scalable background processing.

## 4. Enable HTTP Compression

Another simple but powerful optimization is **HTTP compression**.

Large JSON responses can consume significant bandwidth and increase latency.

Spring Boot allows response compression through configuration.

Example:

server.compression.enabled=true  
server.compression.mime-types=application/json,application/xml,text/html

Compression reduces payload sizes dramatically, improving network performance.

Benefits include:

- Faster API responses
- Reduced bandwidth usage
- Improved mobile performance

This is especially useful for APIs returning large JSON payloads.

## 5. Use Connection Pooling

Database connections are expensive resources. Opening a new connection for every request can significantly slow down the application.

Spring Boot uses **HikariCP** as its default connection pool.

Connection pools maintain a set of reusable connections that improve performance.

Example configuration:

spring.datasource.hikari.maximum-pool-size=20  
spring.datasource.hikari.minimum-idle=5

Benefits include:

- Reduced connection latency
- Better database resource utilization
- Improved throughput

Without proper connection pooling, even high-end databases can become overwhelmed under heavy traffic.

## 6. Optimize JSON Serialization

Serialization is another hidden performance cost in APIs.

Spring Boot typically uses **Jackson** for converting Java objects to JSON.

Large or complex objects can slow down serialization.

To optimize:

- Avoid returning unnecessary fields
- Use DTOs instead of entities
- Enable Jackson performance modules

Example dependency:

<dependency>  
  <groupId>com.fasterxml.jackson.module</groupId>  
  <artifactId>jackson-module-afterburner</artifactId>  
</dependency>

Jackson Afterburner improves serialization performance significantly.

You can also:

- Use record classes for lightweight models
- Reduce nested object structures

Efficient serialization leads to faster API responses and lower CPU usage.

## 7. Tune the JVM and Thread Pool

The Java Virtual Machine plays a major role in application performance.

Default JVM settings are not always optimized for production workloads.

Important JVM tuning strategies include:

- Proper heap size configuration
- Garbage collection tuning
- Thread pool optimization

Example JVM configuration:

-Xms4g  
-Xmx4g  
-XX:+UseG1GC

You should also tune Spring Boot thread pools.

Example configuration:

server.tomcat.threads.max=200  
server.tomcat.threads.min-spare=20

Proper thread pool management ensures the system can handle high request concurrency without thread starvation.

## Bonus Tip: Use API Response Pagination

Returning large datasets in a single response slows down APIs.

Instead of returning thousands of records at once, implement pagination.

Example:

Pageable pageable = PageRequest.of(0, 50);  
Page<Order> orders = orderRepository.findAll(pageable);

Benefits include:

- Faster responses
- Lower memory usage
- Improved client performance

Pagination is essential for scalable APIs.

## Real-World Performance Example

Consider a Spring Boot service handling product search requests.

Before optimization:

- Average response time: 600ms
- Database queries per request: 25
- CPU usage: 80%

After applying:

- Caching
- Query optimization
- Async processing
- Compression

Results:

- Average response time: 60ms
- Database queries per request: 5
- CPU usage: 35%

That’s nearly **10x faster performance** with relatively simple improvements.

## Common Performance Mistakes in Spring Boot APIs

Avoid these common mistakes:

1. Fetching entire database tables
2. Ignoring caching opportunities
3. Blocking API calls unnecessarily
4. Returning overly large JSON payloads
5. Not tuning connection pools
6. Using unoptimized queries
7. Ignoring JVM configuration

Each of these can significantly reduce API performance.

## Recommended Performance Architecture

A high-performance Spring Boot API architecture typically includes:

- Redis caching layer
- Optimized database queries
- Asynchronous background processing
- Connection pooling
- HTTP compression
- Efficient serialization
- JVM tuning

When these components work together, APIs can handle high traffic while maintaining low latency.

## Conclusion

Performance optimization is not about a single trick -it is about identifying and eliminating bottlenecks throughout the system.

The **seven techniques** provide a strong foundation for building fast and scalable APIs:

1. Smart caching
2. Query optimization
3. Asynchronous processing
4. HTTP compression
5. Connection pooling
6. Efficient serialization
7. JVM and thread pool tuning

When applied correctly, these strategies can make your Spring Boot APIs significantly faster and more scalable.


---

# The Spring Boot Production Checklist

What I verify before I let any Spring Boot app touch production:

Connection pool configured for actual load, not default 10.

Entity relationships use EAGER loading where needed or JOIN FETCH queries.

@Transactional only on methods that actually need transactions and don’t call external APIs.

Exception handling re-throws or fails loudly instead of swallowing errors.

Jackson serialization configured with @JsonIgnore or DTOs to prevent circular references.

Separate application-prod.yml with actual production configs, not environment variables scattered everywhere.

Caching only on queries that are actually slow and frequently accessed.

Development environment matches production stack, OS, and Java version.

**These aren’t best practices. These are survival checks.**





---


# @ConfigurationProperties

## 1. What is @ConfigurationProperties Annotation?

While discussing this section, we need to explore the use of the `@Value` annotation and understand its distinctions from other approaches. To do so, we can divide this section into three parts:

1. What is the `@Value` annotation?
2. What is the `@ConfigurationProperties` annotation?
3. The key differences between `@Value` and `@ConfigurationProperties`.

Let’s dive into each part.

### a. What is @Value annotation?

The @Value annotation in Spring Boot is used to inject values from external sources like properties files (application.properties or application.yml), environment variables, or system properties into Spring-managed components (beans). It allows you to wire configuration values into your application easily.  
Example:

import org.springframework.beans.factory.annotation.Value;  
import org.springframework.stereotype.Component;  
  
@Component  
public class AppConfig {  
    // Injecting property value  
    @Value("${app.name}")  
    private String appName;  
    @Value("${app.version}")  
    private String appVersion;  
    // Method to display values  
    public void printAppInfo() {  
        System.out.println("App Name: " + appName);  
        System.out.println("App Version: " + appVersion);  
    }  
}

### b. What is @ConfigurationProperties Annotation?

The **_@ConfigurationProperties_** annotation in Spring Boot is used to link or bind external configuration properties defined in **_application.properties_** or **_application.yml_** to a Plain Old Java Object (POJO).

This simplifies the management of application-specific settings, ensuring a clean and organized approach.

**Key Features**:

1. Lets you directly map configuration values to fields in a Java object.
2. Works with nested properties and complex data structures.
3. Provides type safety with strong validation of properties when the application starts.

### c. Difference between @Value and @ConfigurationProperties annotations.

**@Value Annotation**

- Used for injecting individual values from properties files or environment variables.
- Simple and direct, ideal for one-off injections or when only a few properties are needed.
- Injects a specific property using the property key.

**@ConfigurationProperties Annotation**

- Used to bind a group of related properties to a Java object.
- More powerful for handling multiple, structured properties (e.g., nested properties) at once, and useful for managing complex configurations.
- Binds an entire class to a set of related properties, usually with a common prefix.

## 2. Why Use @ConfigurationProperties Annotation?

Using **_@ConfigurationProperties_** provides several advantages:

- Application configurations are decoupled from the business logic, promoting cleaner code.
- Unlike reading configuration values directly from Environment or **_@Value_** , **_@ConfigurationProperties_** ensures that properties are bound to types correctly.
- It allows for grouping related configurations together under a specific prefix, making the properties easier to manage and understand.

## 3. How @ConfigurationProperties Works

At its core, **_@ConfigurationProperties_** maps property values defined in the configuration file (like `application.yml` or `application.properties`) to fields in a Java class. The key aspect is that you specify a prefix for properties to be bound.

Let’s walk through an example where we use **_@ConfigurationProperties_** to load application-specific settings.

## 4. Example: Using the @ConfigurationProperties Annotation in Spring Boot

> Note:The dependencies used in this project are **Spring Boot Starter Web** and **Lombok**.

### **Step 1: Define Configuration Properties in** `**application.properites file.**`

In this example, we configure the name, description, and version of an application. These are the properties we want to map.

**application**.**properties**

spring.application.name=ConfigMaster  
  
app.name=ConfigApp  
app.description=A site for config management  
app.version=1.0

### **Step 2: Create a POJO to Bind the Properties**

package com.configmaster.config;  
  
import lombok.Data;  
import org.springframework.boot.context.properties.ConfigurationProperties;  
import org.springframework.context.annotation.Configuration;  
  
@Configuration  
@Data  
@ConfigurationProperties(prefix = "app")  
public class AppProperties {  
    private String name;  
    private String description;  
    private String version;  
}

In this class, the **_@ConfigurationProperties_** annotation indicates that the properties with the prefix `"app"` should be bound to the fields of `AppProperties`.

### Step 3: Injecting the Configuration in Your Application

Now, you can inject the `AppProperties` class wherever it’s needed. For instance, in a controller or service class, you can access the configuration values:

package com.configmaster.api;  
  
import com.configmaster.config.AppProperties;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
@RestController  
@RequestMapping("/config")  
public class ConfigTestApiEndPoint {  
    private final AppProperties appProperties;  
  
    @Autowired  
    public ConfigTestApiEndPoint(AppProperties appProperties) {  
        this.appProperties = appProperties;  
    }  
  
    @GetMapping("/info")  
    public String getAppInfo() {  
        return "App Name: " + appProperties.getName() +  
                ", Description: " + appProperties.getDescription() +  
                ", Version: " + appProperties.getVersion();  
    }  
}

The @**_ConfigTestApiEndPoint_** class accesses the properties defined in **_AppProperties_** and can return them as part of a REST endpoint.

**Output:**

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:963/1*XrabgrCGw73BR5FEi8q22A.png)

## Conclusion

- The `@ConfigurationProperties` annotation in Spring Boot offers a powerful and flexible way to manage external configuration properties.
- It promotes better separation of concerns by decoupling configuration from business logic, ensures type safety, and allows for easy validation of property values.
- By leveraging this annotation, you can keep your code clean, maintainable, and scalable, making it a crucial part of modern Spring Boot applications.
- For any project that requires organized management of configuration data, `@ConfigurationProperties` is a must-use tool!


---
# Designing Idempotent APIs in Spring Boot

In RESTful API design, **idempotency** is a crucial concept that ensures safety and consistency, especially for **POST**, **PUT**, or **DELETE** operations. It guarantees that **repeating the same request multiple times has the same effect as making it once**. This is essential in distributed systems where network retries are common.

In this blog, we’ll explore:

- ✅ What is idempotency?
- 🔁 Why idempotency matters in API design
- 🛠️ How to implement idempotency in Spring Boot
- 🔐 Using `Idempotency-Key`
- 💡 Best practices
- 📦 Example code snippets

---

## [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#what-is-idempotency)🤔 What is Idempotency?

An API is **idempotent** if multiple identical requests result in the **same server state** and **same response**.

|HTTP Method|Idempotent?|Description|
|---|---|---|
|GET|✅|Safe, no changes|
|PUT|✅|Overwrites the resource|
|DELETE|✅|Removes the resource (multiple calls have the same result)|
|POST|❌ (by default)|Usually creates new resources (can be made idempotent)|

---

## [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#why-idempotency-matters)🔁 Why Idempotency Matters

Imagine a **money transfer** API that retries due to a timeout. Without idempotency, it may **deduct the amount multiple times**. Idempotency ensures:

- Data consistency
- Retry safety
- Easier debugging
- Enhanced user trust

---

## [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#how-to-design-idempotent-apis-in-spring-boot)🛠️ How to Design Idempotent APIs in Spring Boot

### [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#1-use-idempotency-key-for-post-requests)✅ 1. Use Idempotency Key (For POST requests)

- Clients send a unique `Idempotency-Key` in the header.
- Server stores the response associated with the key.
- Repeated requests with the same key return the **stored response** without executing the logic again.

### [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#sample-header)🔐 Sample Header

```
POST /api/payments
Idempotency-Key: abc123
```

---

## [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#example-implementation-in-spring-boot)🧪 Example Implementation in Spring Boot

### [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#step-1-create-entity-for-storing-keys)Step 1: Create Entity for Storing Keys

```
@Entity
public class IdempotencyRecord {
    @Id
    private String key;
    private String responseBody;
    private int statusCode;
}
```

### [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#step-2-create-repository)Step 2: Create Repository

```
@Repository
public interface IdempotencyRepository extends JpaRepository<IdempotencyRecord, String> {}
```

### [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#step-3-create-filter-or-interceptor)Step 3: Create Filter or Interceptor

```
@Component
public class IdempotencyInterceptor implements HandlerInterceptor {

    @Autowired
    private IdempotencyRepository repository;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws IOException {
        String key = request.getHeader("Idempotency-Key");
        if (key != null && repository.findById(key).isPresent()) {
            IdempotencyRecord record = repository.findById(key).get();
            response.setStatus(record.getStatusCode());
            response.getWriter().write(record.getResponseBody());
            return false; // Stop processing
        }
        return true; // Continue
    }
}
```

### [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#step-4-store-response-after-processing)Step 4: Store Response After Processing

In your controller or service layer:  

```
@PostMapping("/payments")
public ResponseEntity<String> makePayment(@RequestBody PaymentRequest request,
                                          @RequestHeader("Idempotency-Key") String key) {
    String response = "Payment of " + request.getAmount() + " successful!";

    // Save idempotent record
    IdempotencyRecord record = new IdempotencyRecord();
    record.setKey(key);
    record.setResponseBody(response);
    record.setStatusCode(200);
    repository.save(record);

    return ResponseEntity.ok(response);
}
```

---

## [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#caution)⚠️ Caution

- TTL (Time-to-live): Store keys temporarily to prevent DB bloat.
- Uniqueness: Ensure `Idempotency-Key` is truly unique per action.
- Side effects: Avoid non-idempotent side-effects like sending emails in retries.

---

## [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#best-practices)🧠 Best Practices

- Make APIs idempotent **by default** where possible.
- Return consistent response codes.
- Store metadata for debugging.
- Educate API consumers on using `Idempotency-Key`.

---

## [](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi#realworld-use-cases)🧩 Real-World Use Cases

- Payment processing (Stripe, Razorpay)
- Booking engines
- Order creation
- Email subscriptions

---

✅ Conclusion

Designing idempotent APIs is a must for building robust, production-grade systems. It prevents data duplication, race conditions, and unexpected behaviors—especially in failure scenarios. With a simple interceptor and repository, you can build **idempotent POST APIs in Spring Boot** easily.



---



# ETags


ETags are a feature built into HTTP that help with caching and reduce unnecessary data transfers. An ETag is a string that represents the state of a resource at a certain point in time. Clients can use this value to check if the resource has changed before downloading it again. Spring Boot supports ETags and makes it possible to send them with responses and process the `If-None-Match` request header to handle conditional requests.

_I publish free articles like this daily, if you want to support my work and get access to exclusive content and weekly recaps, consider subscribing to my_ [_Substack_](https://alexanderobregon.substack.com/)_._

## How ETags Work with HTTP

When a browser or API client makes requests to a server, it often asks for the same resource multiple times. Without any caching mechanism, the server would always send back the entire response, even if nothing has changed. ETags give both sides a lightweight way to avoid this waste by marking each version of a resource with an identifier. That identifier changes whenever the resource changes, which makes it possible to ask the server whether the current copy is still valid before transferring data again.

### What an ETag Represents

An ETag is essentially a token that identifies a particular version of a resource. The server generates it based on content, a timestamp, or even a version number stored in a database. When the resource is updated, the ETag changes, which lets the client detect differences across requests. ETags come in two flavors: strong and weak. Strong ETags signal that the representation is byte-for-byte identical, while weak ETags allow for semantically equivalent content that may not be exactly the same at the byte level. Weak ETags are prefixed with `W/`, while strong ones are just quoted strings.

Here’s a simple example of how a server might attach an ETag in raw HTTP:

HTTP/1.1 200 OK  
Content-Type: application/json  
ETag: "e4f1a9d7"  
Content-Length: 46  
  
{"id":1,"name":"Document A","status":"active"}

If the content changes, the server produces a new ETag, so the next request will get something different like `"9a13f882"`.

In a Java context, you could generate an ETag from content using a hash function.

import java.security.MessageDigest;  
import java.util.HexFormat;  
import java.nio.charset.StandardCharsets;  
  
public class EtagGenerator {  
    public static String generate(String content) throws Exception {  
        MessageDigest md = MessageDigest.getInstance("MD5");  
        byte[] digest = md.digest(content.getBytes(StandardCharsets.UTF_8));  
        return "\"" + HexFormat.of().formatHex(digest) + "\"";  
    }  
}

Here, an MD5 hash of the content is wrapped in quotes to match the HTTP spec. While MD5 isn’t secure for cryptography, it’s fine for ETag generation because the goal is to detect changes, not protect data.

Another strategy is to generate ETags from database version columns. A table row with an incrementing `version` field can produce a stable ETag until the resource changes.

String etag = "\"" + document.getVersion() + "\"";

That avoids hashing large blobs of data and ties the ETag directly to how your application tracks resource changes.

### The If-None-Match Header

When a client has received an ETag, it can use it in later requests by setting the `If-None-Match` header. This tells the server, “only send me the full resource if it has changed.” The server checks the header against its current ETag and decides whether to return the full content or a lightweight response.

Here’s how a conditional request looks in raw HTTP:

GET /document/1 HTTP/1.1  
Host: api.example.com  
If-None-Match: "e4f1a9d7"

If the resource is still the same, the server replies with:

HTTP/1.1 304 Not Modified  
ETag: "e4f1a9d7"

No body is sent because the client already has the correct version. This saves bandwidth and processing on both sides.

In Java, handling this behavior can be as direct as comparing strings.

import org.springframework.http.HttpHeaders;  
import org.springframework.http.HttpStatus;  
import org.springframework.http.ResponseEntity;  
  
public ResponseEntity<String> handleRequest(String currentContent, String ifNoneMatchHeader) {  
    String etag = "\"" + Integer.toHexString(currentContent.hashCode()) + "\"";  
    if (matchesIfNoneMatch(etag, ifNoneMatchHeader)) {  
        return ResponseEntity.status(HttpStatus.NOT_MODIFIED).eTag(etag).build();  
    }  
    return ResponseEntity.ok().eTag(etag).body(currentContent);  
}  
  
private boolean matchesIfNoneMatch(String etag, String header) {  
    if (header == null || header.isBlank()) return false;  
    for (String raw : header.split(",")) {  
        String candidate = raw.trim();  
        if ("*".equals(candidate)) return true;       // any current representation  
        if (candidate.startsWith("W/")) candidate = candidate.substring(2).trim(); // weak validators match  
        if (candidate.equals(etag)) return true;      // quoted strong match  
    }  
    return false;  
}

This method shows the actual logic behind conditional requests: generate an identifier, compare it to what the client sent, and decide on the response.

It’s also worth considering that `If-None-Match` can contain multiple ETags separated by commas. This allows clients to provide more than one candidate value in case they’re caching multiple variants of the same resource. A server needs to handle these cases by checking against all provided values.

String ifNoneMatch = "\"abc123\", W/\"def456\", \"ghi789\"";  
boolean match = false;  
for (String raw : ifNoneMatch.split(",")) {  
    String c = raw.trim();  
    if ("*".equals(c)) { match = true; break; }  
    if (c.startsWith("W/")) c = c.substring(2).trim();  
    if (c.equals(etag)) { match = true; break; }  
}  
if (match) {  
    // respond with 304  
}

That small detail often gets overlooked, but it’s part of the HTTP specification and comes into play when dealing with caching proxies or multiple resource variants.

## ETag Support in Spring Boot

Spring Boot is built on top of Spring MVC, and that means a lot of HTTP features are already supported out of the box. ETags are no exception. You can either let the framework take care of generating them automatically through filters or handle the process yourself when you want more control over how the identifiers are produced. Both strategies rely on the same HTTP standards, but they differ in how much responsibility you take in your code.

### Using ShallowEtagHeaderFilter

Spring provides a servlet filter named `ShallowEtagHeaderFilter` that works with very little effort. Its job is to buffer the response, calculate a hash of the response body, and add the `ETag` header before the content goes back to the client. If the request includes `If-None-Match`, the filter compares it against the generated value and may shortcut the response to a `304 Not Modified`.

To turn it on, you register it as a filter in your configuration.

import org.springframework.boot.web.servlet.FilterRegistrationBean;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
import org.springframework.web.filter.ShallowEtagHeaderFilter;  
  
@Configuration  
public class EtagConfig {  
  
    @Bean  
    public FilterRegistrationBean<ShallowEtagHeaderFilter> etagFilter() {  
        FilterRegistrationBean<ShallowEtagHeaderFilter> registration = new FilterRegistrationBean<>();  
        registration.setFilter(new ShallowEtagHeaderFilter());  
        registration.addUrlPatterns("/*");  
        return registration;  
    }  
}

This works well when the response is not too large, because the filter has to buffer it to compute the hash. That can be a drawback for streaming responses where buffering defeats the purpose, but for typical JSON or HTML responses it is very effective.

Another detail to keep in mind is that this filter uses the response bytes as the basis for the ETag. That means if the response content changes even slightly, a new value will be generated. It makes the filter easy to apply, but sometimes it’s more useful to tie the ETag to application-level data rather than the rendered output. That’s where custom strategies come in.

### Generating Custom ETags in Controllers

For applications that want more precise control over caching, generating ETags in the controller or service layer can be a better fit. Instead of hashing the rendered content, you can decide what part of your data determines the freshness of a resource. A version column in a database, a timestamp, or even a UUID that changes on updates can all serve as a stable ETag source.

Here’s a controller that generates its own ETag for a document resource.

import org.springframework.http.HttpHeaders;  
import org.springframework.http.HttpStatus;  
import org.springframework.http.ResponseEntity;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
@RestController  
public class DocumentController {  
  
    @GetMapping("/document")  
    public ResponseEntity<String> getDocument() {  
        String content = "Sample Document Content";  
        String etag = "\"" + Integer.toHexString(content.hashCode()) + "\"";  
  
        HttpHeaders headers = new HttpHeaders();  
        headers.setETag(etag);  
  
        return new ResponseEntity<>(content, headers, HttpStatus.OK);  
    }  
}

The hash is based on the string itself, but you could just as easily use a version field pulled from a database row.

A more data-driven approach could look like this:

String etag = "\"" + record.getVersion() + "\"";  
return ResponseEntity.ok().eTag(etag).body(record.getContent());

This way, the ETag only changes when the underlying record changes. That avoids recalculating values for responses that differ only in formatting or representation, giving you tighter control over client caching.

Custom generation is especially useful when responses are expensive to build. Rather than computing and sending the whole resource to every client, the server can trust its own version tracking and only send data if there’s actually something new.

### Handling Conditional Requests Manually

Sometimes you’ll want to handle `If-None-Match` headers explicitly. Spring makes it possible to read request headers directly in controller methods and respond based on the comparison. This approach works well when you have to combine ETag logic with other checks, or when you want to return slightly different responses depending on context.

Here’s an example of manual handling:

import org.springframework.http.HttpHeaders;  
import org.springframework.http.HttpStatus;  
import org.springframework.http.ResponseEntity;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.RequestHeader;  
import org.springframework.web.bind.annotation.RestController;  
  
@RestController  
public class ProductController {  
  
    @GetMapping("/product")  
    public ResponseEntity<String> getProduct(  
            @RequestHeader(value = HttpHeaders.IF_NONE_MATCH, required = false) String ifNoneMatch) {  
  
        String content = "Product data";  
        String etag = "\"" + Integer.toHexString(content.hashCode()) + "\"";  
  
        if (etag.equals(ifNoneMatch)) {  
            return ResponseEntity.status(HttpStatus.NOT_MODIFIED).eTag(etag).build();  
        }  
        return ResponseEntity.ok().eTag(etag).body(content);  
    }  
}

This method checks if the ETag matches and avoids returning the body if the client already has the current version.

There are also cases where clients send multiple ETags in the `If-None-Match` header. This can happen with caching intermediaries or when clients handle several possible variants of the same resource. To support that, you need to parse the header and check each value.

if (ifNoneMatch != null) {  
    for (String candidate : ifNoneMatch.split(",")) {  
        if (etag.equals(candidate.trim())) {  
            return ResponseEntity.status(HttpStatus.NOT_MODIFIED).eTag(etag).build();  
        }  
    }  
}

This makes sure your application stays compliant with the HTTP specification and works correctly even with shared caches or advanced clients.

### What Happens in the Background

Spring handles ETags through the servlet API and the HTTP specification. No matter if you rely on the filter or handle things directly in a controller, the process is about comparing string values and deciding whether to return the full content or just headers. With the filter in place, the response is wrapped and buffered so a hash can be calculated. That value becomes the ETag header. When a request arrives with `If-None-Match`, the filter does the comparison before the body is sent. If there’s a match, the body is dropped and the server replies with a `304 Not Modified` status and headers only.

Custom logic in controllers works a little differently. `ResponseEntity` makes it easy to attach the ETag without extra effort, while the comparison step remains in your code. Spring then handles serialization of the response once you’ve decided what to return. What ties it all together is the fact that browsers, proxies, and HTTP clients already understand how to deal with ETags. After your application generates them, other parts of the network chain such as CDNs or reverse proxies can respect those values automatically.

## Conclusion

ETags give Spring Boot applications a way to work with HTTP’s conditional request model in a very direct way. The process comes down to producing an identifier, attaching it to responses, and checking it against incoming headers. Whether the value is generated through a filter or custom logic in a controller, the comparison step is simple, yet it drives an efficient exchange between clients and servers. With that in place, browsers, proxies, and CDNs can all work with your application more effectively, reducing the cost of repeated requests without adding much complexity to your code.

---
