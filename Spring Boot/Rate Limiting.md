# Implementing Rate Limiting in a Java REST API

Rate limiting is essential for preventing abuse, managing traffic spikes, and ensuring fair usage of your API. Here are several approaches to implement rate limiting in a Java REST API:

## 1. Using Spring Boot with Bucket4j

**Bucket4j** is a popular rate-limiting library based on token bucket algorithm.

### Implementation:

```java
// Add to pom.xml
<dependency>
    <groupId>com.github.vladimir-bukhtoyarov</groupId>
    <artifactId>bucket4j-core</artifactId>
    <version>7.6.0</version>
</dependency>

// Configuration class
@Configuration
public class RateLimitConfig {
    
    @Bean
    public Bandwidth limit() {
        return Bandwidth.classic(100, Refill.intervally(100, Duration.ofMinutes(1)));
    }
    
    @Bean
    public ProxyManager<String> proxyManager(Bandwidth limit) {
        return new ProxyManager<>() {
            private final Map<String, Bucket> buckets = new ConcurrentHashMap<>();
            
            @Override
            public Bucket resolveProxy(String key) {
                return buckets.computeIfAbsent(key, k -> Bucket.builder()
                    .addLimit(limit)
                    .build());
            }
        };
    }
}

// Filter implementation
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class RateLimitFilter implements Filter {
    
    @Autowired
    private ProxyManager<String> proxyManager;
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 
            throws IOException, ServletException {
        
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        String apiKey = httpRequest.getHeader("API-Key"); // or use IP address
        
        Bucket bucket = proxyManager.resolveProxy(apiKey);
        
        if (bucket.tryConsume(1)) {
            chain.doFilter(request, response);
        } else {
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            httpResponse.setContentType("application/json");
            httpResponse.setStatus(429);
            httpResponse.getWriter().write("{ \"error\": \"Too many requests\" }");
        }
    }
}
```

## 2. Using Spring Cloud Gateway

If you're using Spring Cloud Gateway, you can leverage its built-in rate limiting:

```yaml
# application.yml
spring:
  cloud:
    gateway:
      routes:
      - id: my-service
        uri: http://localhost:8081
        predicates:
        - Path=/api/**
        filters:
        - name: RequestRateLimiter
          args:
            redis-rate-limiter.replenishRate: 100
            redis-rate-limiter.burstCapacity: 200
            redis-rate-limiter.requestedTokens: 1
```

## 3. Using Guava RateLimiter

For simple in-memory rate limiting:

```java
import com.google.common.util.concurrent.RateLimiter;

@Service
public class RateLimiterService {
    private final RateLimiter rateLimiter = RateLimiter.create(100.0); // 100 requests per second
    
    public boolean tryAcquire() {
        return rateLimiter.tryAcquire();
    }
}

@RestController
public class MyController {
    
    @Autowired
    private RateLimiterService rateLimiter;
    
    @GetMapping("/api/resource")
    public ResponseEntity<?> getResource() {
        if (!rateLimiter.tryAcquire()) {
            return ResponseEntity.status(429).body("Too many requests");
        }
        return ResponseEntity.ok("Resource data");
    }
}
```

## 4. Distributed Rate Limiting with Redis

For distributed systems:

```java
@Configuration
public class RedisConfig {
    
    @Bean
    public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, String> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
        return template;
    }
}

@Service
public class RedisRateLimiter {
    
    @Autowired
    private RedisTemplate<String, String> redisTemplate;
    
    public boolean isAllowed(String key, int maxRequests, Duration duration) {
        String redisKey = "rate_limit:" + key;
        
        Long current = redisTemplate.opsForValue().increment(redisKey);
        if (current == 1) {
            redisTemplate.expire(redisKey, duration.getSeconds(), TimeUnit.SECONDS);
        }
        
        return current <= maxRequests;
    }
}

// Usage in controller
@GetMapping("/api/resource")
public ResponseEntity<?> getResource(@RequestHeader("API-Key") String apiKey) {
    if (!redisRateLimiter.isAllowed(apiKey, 100, Duration.ofMinutes(1))) {
        return ResponseEntity.status(429).build();
    }
    return ResponseEntity.ok("Resource data");
}
```

## 5. Using Resilience4j

Resilience4j provides rate limiting as part of its fault tolerance features:

```java
// Configuration
@Bean
public RateLimiterConfig rateLimiterConfig() {
    return RateLimiterConfig.custom()
        .limitRefreshPeriod(Duration.ofSeconds(1))
        .limitForPeriod(100) // 100 requests per second
        .timeoutDuration(Duration.ofMillis(100))
        .build();
}

@Bean
public RateLimiterRegistry rateLimiterRegistry(RateLimiterConfig config) {
    return RateLimiterRegistry.of(config);
}

// Usage
@GetMapping("/api/resource")
public String getResource() {
    RateLimiter rateLimiter = rateLimiterRegistry.rateLimiter("resourceLimiter");
    Callable<String> restrictedCall = RateLimiter.decorateCallable(
        rateLimiter, 
        () -> "Resource data"
    );
    
    try {
        return restrictedCall.call();
    } catch (RequestNotPermitted e) {
        throw new ResponseStatusException(HttpStatus.TOO_MANY_REQUESTS, "Rate limit exceeded");
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```

## Best Practices

1. **Identify rate limiting keys**: Use API keys, IP addresses, or user IDs
2. **Return proper headers**:
   ```java
   response.setHeader("X-Rate-Limit-Limit", "100");
   response.setHeader("X-Rate-Limit-Remaining", String.valueOf(remaining));
   response.setHeader("X-Rate-Limit-Reset", String.valueOf(resetTime));
   ```
3. **Consider different limits** for different endpoints or user tiers
4. **Log rate limit violations** for monitoring and analytics
5. **Combine with caching** to reduce load when rate limits are hit

Choose the approach that best fits your architecture - in-memory for simple apps, Redis for distributed systems, or dedicated libraries for more complex scenarios.