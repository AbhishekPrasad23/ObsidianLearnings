


![](https://images.ctfassets.net/23aumh6u8s0i/6PHOLRqEbX3rVvwUyufoeK/125762bd3ecc31d99d61b24f2a2987d8/spring-new.png)



Slow performance is a recurring and complex problem that developers are often faced with. One of the most common approaches to address such a problem is through caching. Indeed, this mechanism allows achieving a substantial improvement in the performance of any type of application. The problem is that dealing with caching is not an easy task. Luckily, caching is provided by Spring Boot transparently thanks to the Spring Boot Cache Abstraction, which is a mechanism allowing consistent use of various caching methods with minimal impact on the code. Let's see everything you should know to start dealing with it.

First, we will introduce the concept of caching. Then, we will study the most common Spring Boot cache-related annotations, understanding what the most important ones are, where, and how to use them. Next, it will be time to see what are the most popular cache engines supported by Spring Boot at the time of writing. Finally, we will see Spring Boot caching in action through an example.

## What is Caching

Caching is a mechanism aimed at enhancing the performance of any kind of application. It relies on a cache, which can be seen as a temporary fast access software or hardware component that stores data to reduce the time required to serve future requests related to the same data. Dealing with caching is complex, but mastering this concept is practically unavoidable for any developer. If you are interested in delving into caching, understanding what it is, how it works, and what are its most important types, you should follow [this](https://auth0.com/blog/what-is-caching-and-how-it-works/) link first.

## Getting Started

The Spring Boot [Cache Abstraction](https://docs.spring.io/spring-framework/docs/5.0.0.M5/spring-framework-reference/html/cache.html) does not come with the framework natively but requires a few dependencies. Thankfully, you can easily install all of them by adding the [`spring-boot-starter-cache`](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-cache) to your dependencies.

If you are a Gradle user, add this dependency to your `build.gradle` file:

```
implementation "org.springframework.boot:spring-boot-starter-cache:2.5.0"
```

While if you are a Maven user, add the following dependency to your `pom.xml` file:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
    <version>2.5.0</version>
</dependency>
```

In detail, `spring-boot-starter-cache` brings the [`spring-context-support`](https://mvnrepository.com/artifact/org.springframework/spring-context-support) module, which transitively depends on [`spring-context`](https://mvnrepository.com/artifact/org.springframework/spring-context). The latter allows Spring to deal with contexts, also called Spring IoC ([Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control)) containers, which are responsible for managing objects in a Spring application. In particular, they are in charge of configuring, instantiating, and assembling Beans by reading configuration files and employing annotations. While the `spring-context-support` module provides support for integrating third-party cache engines, which will be presented later, into a Spring application. Follow [this](https://docs.spring.io/spring-framework/docs/5.0.0.M5/spring-framework-reference/html/beans.html) link from the Spring official documentation for further reading on the Spring IoC containers and the bean management.

## Spring Boot Caching Annotations

After adding the dependencies required to start using the Spring Cache Abstraction mechanism, it is time to see how to implement the desired caching logic. This can easily be achieved by marking Java methods with specific caching-related annotations. Let's delve into the most important ones, showing where to use them and how.

### [@EnableCaching](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/EnableCaching.html)

To enable the Spring Boot caching feature, you need to add the `@EnableCaching` annotation to any of your classes annotated with `@Configuration` or to the boot application class annotated with `@SpringBootApplication`.

```java
@SpringBootApplication  
@EnableCaching   
public class SpringBootCachingApplication {  
  public static void main(String[] args) {  
    SpringApplication.run(SpringBootCachingApplication.class, args);  
  }  
}  
```

`@EnableCaching` automatically sets up a valid instance of the [`CacheManager`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/CacheManager.html) interface, which is needed to enable caching. In particular, this annotation look for one of the many cache engines we will analyze later in the article. If not found, it creates a [`ConcurrentMapCacheManager`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/concurrent/ConcurrentMapCacheManager.html), which provides a default implementation of an in-memory cache based on a [`ConcurrentHashMap`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html) object.

### [@Cacheable](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/Cacheable.html)

This method-level annotation lets Spring Boot know that the return value of the annotated method can be cached. Each time a method marked with this `@Cacheable` is called, the caching behavior will be applied. In particular, Spring Boot will check whether the method has been already invoked for the given arguments. This involves looking for a key, which is generated using the method parameters by default. If no value is found in the cache related to the method for the computed key, the target method will be executed normally. Otherwise, the cached value will be returned immediately. `@Cacheable` comes with many parameters, but the simplest way to use it is to annotate a method with the annotation and parameterize it with the name of the cache where the results are going to be stored.

```java
@Cacheable("authors")
public List<Author> getAuthors(List<Int> ids) { ... }
```

You can also specify how the key that uniquely identifies each entry in the cache should be generated by harnessing the _key_ attribute.

```java
@Cacheable(value="book", key="#isbn")
public Book findBookByISBN(String isbn) { ... }

@Cacheable(value="books", key="#author.id")
public Books findBooksByAuthor(Author author) { ... }
```

Lastly, it is also possible to enable conditional caching as in the following example:

```java
// caching only authors whose full name is less than 15 carachters
@Cacheable(value="authors", condition="#fullName.length < 15")
public Authors findAuthorsByFullName(String fullName) { ... }
```

### [@CachePut](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/CachePut.html)

This method-level annotation should be used when you want to update (put) the cache without avoiding the method from being executed. This means that the method will always be executed — according to the `@CachePut` options — and its result will be stored in the cache. The main difference between `@Cacheable` and `@CachePut` is that the first might avoid executing the method, while the second will run the method and put its results in the cache, even if there is already an existing key associated with the given parameters. Since they have different behaviors, annotating the same method with both `@CachePut` and `@Cacheable` should be avoided.

### [@CacheEvict](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/CacheEvict.html)

This method-level annotation allows you to remove (evict) data previously stored in the cache. By annotating a method with `@CacheEvict` you can specify the removal of one or all values so that fresh values can be loaded into the cache again. If you want to remove a specific value, you should pass the cache key as an argument to the annotation, as in the following example:

```java
@CacheEvict(value="authors", key="#authorId")
public void evictSingleAuthor(Int authorId) { ... }
```

While if you want to clear an entire cache you can the parameter `allEntries` in conjunction with the name of cache to be cleared:

```java
@CacheEvict(value="authors", allEntries=true)
public String evictAllAuthorsCached() { ... }
```

This annotation is extremely important because size is the main problem of caches. A possible solution to this problem is to compress data before caching, as explained [here](https://betterprogramming.pub/how-to-add-compression-to-caching-in-spring-boot-d4d21533167c). On the other hand, the best approach should be to avoid keeping data that you are not using too often in the caches. In fact, since caches can become large very quickly, you should update stale data with `@CachePut` and remove unused data with `@CacheEvict`. In particular, having a method to clean all the caches of your Spring Boot application easily can become essential. If you are interested in implementing an API aimed at this, you can follow [this](https://levelup.gitconnected.com/building-an-api-to-clear-all-the-caches-of-your-spring-boot-application-2d0dfdfe71b3) tutorial.

### [@CacheConfig](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/CacheConfig.html)

This class-level annotation allows you to specify some of the cache configurations in one place, so you do not have to repeat them multiple times:

```java
@CacheConfig(cacheNames={"authors"})
public class AuthorDAO {
    @Cacheable
    publicList<Author> findAuthorsByFullName(String fullName) { ... }
    
    @Cacheable
    public List<Author> findAuthorsByBook(Book book) { ... }
    
    // ...
}
```

## Supported Cache Providers

As mentioned earlier, Spring Boot uses a [`ConcurrentMapCacheManager`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/concurrent/ConcurrentMapCacheManager.html) object as the default cache engine, but it also provides integration with many third-party cache providers. Let's have a brief look at the most important ones.

For more detailed information about the cache providers supported by Spring Boot, read [this](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-caching.html#boot-features-caching-provider) page from the official documentation.

### [Hazelcast](https://hazelcast.com/)

Hazelcast provides a cache-as-a-service for scalable, reliable, and fast caching. By using Hazelcast, developers can separate the caching layer from the application layer without worrying about the cache implementation.

#### Gradle dependency

```
implementation "com.hazelcast:hazelcast-all:4.0.2"
```

#### Maven dependency

```xml
<dependency>
    <groupId>com.hazelcast</groupId>
    <artifactId>hazelcast-all</artifactId>
    <version>4.0.2</version>
</dependency>
 
```

### [EhCache](https://www.ehcache.org/)

Ehcache provides an implementation of the [JCache JSR-107](https://www.jcp.org/en/jsr/detail?id=107) standard, and it is the most widely-used Java-based cache manager, well known for being robust, full-featured, and easy to integrate with popular frameworks.

#### Gradle dependencies

```
implementation "javax.cache:cache-api:1.1.1"
implementation "org.ehcache:ehcache:3.9.4"
```

#### Maven dependencies

```xml
<dependency>
    <groupId>javax.cache</groupId>
    <artifactId>cache-api</artifactId>
    <version>1.1.1</version>
</dependency>
<dependency>
    <groupId>org.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>3.9.4</version>
</dependency>
```

### [Infinispan](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-caching.html#boot-features-caching-provider-hazelcast)

Infinispan is an in-memory data grid that offers features for storing, managing, and processing data. It provides a key/value data store that can hold all types of data and be used for caching.

### Gradle dependency

#### Embedded Mode

```
implementation "org.infinispan:infinispan-spring-boot-starter-embedded:12.1.4.Final"
```

#### Remote Client/Server Mode

```
implementation "org.infinispan:infinispan-spring-boot-starter-remote:12.1.4.Final"
```

### Maven dependency

#### Embedded Mode

```xml
<dependency>
  <groupId>org.infinispan</groupId>
  <artifactId>infinispan-spring-boot-starter-embedded</artifactId>
  <version>12.1.4.Final</version>
</dependency>
```

#### Remote Client/Server Mode

```xml
<dependency>
  <groupId>org.infinispan</groupId>
  <artifactId>infinispan-spring-boot-starter-remote</artifactId>
  <version>12.1.4.Final</version>
</dependency>
```

### [Redis](https://redis.io/)

Redis is an in-memory key-value data structure store that can be used as a database, cache, and message broker.

#### Gradle dependency

```
implementation "org.springframework.boot:spring-boot-starter-data-redis:2.5.0"
```

#### Maven dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.5.0</version>
</dependency>
```

  

### [Caffeine](https://github.com/ben-manes/caffeine)

Caffeine is a high-performance, near-optimal caching library providing an in-memory cache using a [Google Guava](https://en.wikipedia.org/wiki/Google_Guava) inspired API.

#### Gradle dependency

```
implementation "com.github.ben-manes.caffeine:caffeine:3.0.2"
```

#### Maven dependency

```xml
<dependency>
    <groupId>com.github.ben-manes.caffeine</groupId>
    <artifactId>caffeine</artifactId>
    <version>3.0.2</version>
</dependency>
```

### [Couchbase](https://www.couchbase.com/)

Couchbase is a distributed NoSQL cloud database that also offers a fully integrated caching layer, providing high-speed data access.

#### Gradle dependency

```
implementation "com.couchbase.client:java-client:3.1.5"
```

#### Maven dependency

```xml
<dependency>
   <groupId>com.couchbase.client</groupId>
   <artifactId>java-client</artifactId>
   <version>3.1.5</version>
</dependency>
```

## Spring Boot Caching in Action

Let's see Spring Boot caching in action with an example. You can either clone the [GitHub repository that supports this article](https://github.com/Tonel/spring-boot-caching-auth0) or continue following this tutorial.

The demo project consists of a single REST service reachable from a GET request. Such an API will return a list of `Author` objects, whose retrieval logic will be simulated thanks to a 3-second delay. When hit for the first time, the API will take more than 3 seconds to respond. On the contrary, on successive calls, the response will be almost instantaneous. This is the effect of the Spring Boot caching system, thanks to which the slow API logic will be avoided when it is possible to retrieve the desired data from a cache.

```java
@Getter
@Setter
public class Author {
    private static int id = 0;
    private String name;
    private String surname;
    private String birthDate;

    public Author() {}

    public Author(
            String name,
            String surname,
            String birthDate
    ) {
        id++;
        this.name = name;
        this.surname = surname;
        this.birthDate = birthDate;
    }
}
```

The [`@Getter`](https://projectlombok.org/features/GetterSetter) and [`@Setter`](https://projectlombok.org/features/GetterSetter) annotations used in the code examples above are part of the [Project Lombok](https://projectlombok.org/). They are used to automatically generate getters and setters. This is not mandatory and just an additional way to avoid boilerplate code.

```java
@Service
public class AuthorService {

    @Cacheable("authors")
    public List<Author> getAll() {
        // simulating a delay due to the data retrieval operation
        try  {
            System.out.println("Retrieving all the authors...");
            Thread.sleep(3000);
        }
        catch (InterruptedException e) {
            e.printStackTrace();
        }

        // returning a list containing all the authors
        return Arrays.asList(
                new Author("Patricia", "Brown", null),
                new Author("James", "Smith", "1964-07-01"),
                new Author("Mary", "Williams", "1988-11-19")
        );
    }
}
```

Please, note that the `retrieveAll()` service layer method is annotated with `@Cacheable("authors")`. This way, the data returned from this method will be stored in a cache named "authors", as explained earlier.

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/authors")
public class AuthorController {
    private final AuthorService authorService;

@GetMapping
    public ResponseEntity<List<Author>> getAll() {
        loggingMessage("Request received!");
        // retrieving the desired data
        List<Author> authors = authorService.findAll();
        loggingMessage("Data retrieved!");

        return new ResponseEntity<>(
                authors,
                HttpStatus.OK
        );
    }

    private void loggingMessage(
            String message
    ) {
        System.out.printf("[%s] %s%n", java.time.LocalTime.now().truncatedTo(ChronoUnit.MILLIS), message);
    }
}
```

As you can see from the log below, the first time `http://localhost:8080/authors` is hit, the response time is more than 3 seconds, and the data returned from `retrieveAll` will be stored in the cache name _authors_. Then, when the endpoint is hit again, the desired data is returned immediately, and the method is not executed, as you notice from the missing data retrieving logging message.

```
[21:23:37.919] Request received!
Retrieving all the authors...
[21:23:40.990] Data retrieved!
    
[21:23:42.844] Request received!
[21:23:42.846] Data retrieved!
```

