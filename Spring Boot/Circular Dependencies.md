# Handling Circular Dependencies in Spring

Circular dependencies occur when two or more beans depend on each other directly or indirectly (Bean A → Bean B → Bean A). Spring can handle some circular dependencies, but it's generally considered bad design and can lead to problems.

## How Spring Handles Circular Dependencies

Spring uses a three-level cache to resolve some circular dependencies during bean initialization:
1. **Singleton Objects Cache** - Fully initialized beans
2. **Early Singleton Objects Cache** - Raw instantiated beans (populated after constructor but before properties)
3. **Singleton Factories Cache** - Object factories that can create early references

## Common Solutions

### 1. Constructor Injection (Recommended)
Spring can't resolve circular dependencies when using **constructor injection** - it will throw a `BeanCurrentlyInCreationException`. This forces you to redesign your components properly.

```java
// This will fail with circular dependency
@Service
public class ServiceA {
    private final ServiceB serviceB;
    
    public ServiceA(ServiceB serviceB) {
        this.serviceB = serviceB;
    }
}

@Service
public class ServiceB {
    private final ServiceA serviceA;
    
    public ServiceB(ServiceA serviceA) {
        this.serviceA = serviceA;
    }
}
```

### 2. Setter/Field Injection (Less Ideal)
Spring can resolve some circular dependencies with setter or field injection because it can first create the bean instance and then inject the dependencies.

```java
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
```

## Better Design Alternatives

### 1. Refactor to Remove Circular Dependency
- Extract common functionality to a third service
- Use interface-based design
- Apply the Dependency Inversion Principle

### 2. Use @Lazy
Mark one of the dependencies as `@Lazy` to break the initialization cycle:

```java
@Service
public class ServiceA {
    private final ServiceB serviceB;
    
    public ServiceA(@Lazy ServiceB serviceB) {
        this.serviceB = serviceB;
    }
}
```

### 3. Use ApplicationContextAware
Manually fetch one of the beans from the context after initialization:

```java
@Service
public class ServiceA implements ApplicationContextAware {
    private ApplicationContext context;
    private ServiceB serviceB;
    
    @PostConstruct
    public void init() {
        this.serviceB = context.getBean(ServiceB.class);
    }
    
    @Override
    public void setApplicationContext(ApplicationContext context) {
        this.context = context;
    }
}
```

### 4. Use Method Injection with @Lookup
For prototype-scoped beans in a circular dependency:

```java
@Service
public class ServiceA {
    public void doSomething() {
        ServiceB serviceB = getServiceB();
        // ...
    }
    
    @Lookup
    protected ServiceB getServiceB() {
        return null; // Implementation provided by Spring
    }
}
```

## Best Practice

The best approach is to **redesign your components** to avoid circular dependencies entirely, as they often indicate a design flaw. Consider:
- Breaking down large services
- Creating intermediary services
- Using event-driven architecture
- Applying domain-driven design principles

Circular dependencies make code harder to test, maintain, and reason about, so they should be avoided when possible.