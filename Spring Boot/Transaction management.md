Transaction management in Spring Boot is significantly simplified compared to traditional Spring applications. Here's a comprehensive guide:

## Auto-Configuration

Spring Boot automatically configures transaction management when it detects relevant dependencies on the classpath:

```xml
<!-- For JPA/Hibernate -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- For JDBC -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

Spring Boot automatically creates:

- `PlatformTransactionManager` bean
- Enables `@EnableTransactionManagement`
- Configures appropriate transaction manager based on available dependencies

## Declarative Transaction Management

### Basic @Transactional Usage

```java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Transactional
    public User createUser(User user) {
        // This method runs in a transaction
        User savedUser = userRepository.save(user);
        
        // If any exception occurs, transaction will rollback
        if (savedUser.getEmail() == null) {
            throw new IllegalArgumentException("Email cannot be null");
        }
        
        return savedUser;
    }
}
```

### Advanced @Transactional Configuration

```java
@Service
public class OrderService {
    
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private InventoryService inventoryService;
    
    @Autowired
    private PaymentService paymentService;
    
    @Transactional(
        propagation = Propagation.REQUIRED,
        isolation = Isolation.READ_COMMITTED,
        timeout = 30,
        rollbackFor = {Exception.class},
        noRollbackFor = {IllegalArgumentException.class}
    )
    public Order processOrder(OrderRequest request) {
        Order order = new Order();
        order.setCustomerId(request.getCustomerId());
        order.setAmount(request.getAmount());
        
        // Save order
        Order savedOrder = orderRepository.save(order);
        
        // Update inventory
        inventoryService.updateStock(request.getProductId(), request.getQuantity());
        
        // Process payment
        paymentService.processPayment(request.getPaymentDetails());
        
        return savedOrder;
    }
}
```

## Transaction Propagation Examples

```java
@Service
public class BusinessService {
    
    @Autowired
    private AuditService auditService;
    
    @Transactional
    public void businessMethod() {
        // This runs in Transaction A
        performBusinessLogic();
        
        // This will join Transaction A (default REQUIRED)
        auditService.logActivity("Business operation completed");
        
        // This will create new Transaction B
        auditService.logCriticalEvent("Critical event");
    }
}

@Service
public class AuditService {
    
    @Transactional(propagation = Propagation.REQUIRED)
    public void logActivity(String message) {
        // Joins existing transaction
        // If parent transaction rolls back, this rolls back too
    }
    
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void logCriticalEvent(String message) {
        // Always creates new transaction
        // This will commit even if parent transaction rolls back
    }
}
```

## Programmatic Transaction Management

### Using TransactionTemplate

```java
@Service
public class PaymentService {
    
    @Autowired
    private TransactionTemplate transactionTemplate;
    
    @Autowired
    private PaymentRepository paymentRepository;
    
    public PaymentResult processPayment(PaymentRequest request) {
        return transactionTemplate.execute(status -> {
            try {
                Payment payment = new Payment();
                payment.setAmount(request.getAmount());
                payment.setAccountId(request.getAccountId());
                
                Payment savedPayment = paymentRepository.save(payment);
                
                // Call external payment gateway
                ExternalPaymentResult result = callPaymentGateway(request);
                
                if (!result.isSuccessful()) {
                    // This will cause rollback
                    throw new PaymentException("Payment failed: " + result.getErrorMessage());
                }
                
                savedPayment.setTransactionId(result.getTransactionId());
                savedPayment.setStatus(PaymentStatus.COMPLETED);
                
                return new PaymentResult(savedPayment, true);
                
            } catch (Exception e) {
                // Transaction will be rolled back
                return new PaymentResult(null, false);
            }
        });
    }
}
```

### Using PlatformTransactionManager

```java
@Service
public class DataMigrationService {
    
    @Autowired
    private PlatformTransactionManager transactionManager;
    
    public void migrateBatchData(List<DataRecord> records) {
        TransactionDefinition definition = new DefaultTransactionDefinition();
        TransactionStatus status = transactionManager.getTransaction(definition);
        
        try {
            for (DataRecord record : records) {
                processRecord(record);
            }
            transactionManager.commit(status);
        } catch (Exception e) {
            transactionManager.rollback(status);
            throw new DataMigrationException("Migration failed", e);
        }
    }
}
```

## Multiple Transaction Managers

When you have multiple data sources:

```java
@Configuration
public class TransactionConfig {
    
    @Bean
    @Primary
    public PlatformTransactionManager primaryTransactionManager(
            @Qualifier("primaryDataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
    
    @Bean
    public PlatformTransactionManager secondaryTransactionManager(
            @Qualifier("secondaryDataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
}

@Service
public class MultiDbService {
    
    @Transactional("primaryTransactionManager")
    public void updatePrimaryDb() {
        // Uses primary transaction manager
    }
    
    @Transactional("secondaryTransactionManager")
    public void updateSecondaryDb() {
        // Uses secondary transaction manager
    }
}
```

## JPA/Hibernate Integration

```java
@Entity
public class Account {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String accountNumber;
    private BigDecimal balance;
    
    // getters and setters
}

@Repository
public interface AccountRepository extends JpaRepository<Account, Long> {
    Optional<Account> findByAccountNumber(String accountNumber);
}

@Service
public class BankService {
    
    @Autowired
    private AccountRepository accountRepository;
    
    @Transactional
    public void transferMoney(String fromAccount, String toAccount, BigDecimal amount) {
        Account from = accountRepository.findByAccountNumber(fromAccount)
            .orElseThrow(() -> new AccountNotFoundException("Account not found: " + fromAccount));
        
        Account to = accountRepository.findByAccountNumber(toAccount)
            .orElseThrow(() -> new AccountNotFoundException("Account not found: " + toAccount));
        
        if (from.getBalance().compareTo(amount) < 0) {
            throw new InsufficientFundsException("Insufficient balance");
        }
        
        from.setBalance(from.getBalance().subtract(amount));
        to.setBalance(to.getBalance().add(amount));
        
        accountRepository.save(from);
        accountRepository.save(to);
        
        // If any exception occurs above, entire transaction rolls back
    }
}
```

## Configuration Properties

```properties
# application.properties

# Transaction timeout (seconds)
spring.transaction.default-timeout=30

# JPA transaction properties
spring.jpa.properties.hibernate.connection.autocommit=false
spring.jpa.properties.hibernate.transaction.coordinator_class=jta

# Connection pool settings affecting transactions
spring.datasource.hikari.auto-commit=false
spring.datasource.hikari.connection-timeout=20000
```

## Testing Transactions

```java
@SpringBootTest
@Transactional
class TransactionTest {
    
    @Autowired
    private UserService userService;
    
    @Autowired
    private UserRepository userRepository;
    
    @Test
    @Rollback(false) // Don't rollback this test transaction
    void testUserCreation() {
        User user = new User("john@example.com", "John Doe");
        User savedUser = userService.createUser(user);
        
        assertThat(savedUser.getId()).isNotNull();
    }
    
    @Test
    void testTransactionRollback() {
        assertThatThrownBy(() -> {
            userService.createInvalidUser(); // This should rollback
        }).isInstanceOf(ValidationException.class);
        
        // Verify rollback occurred
        assertThat(userRepository.count()).isEqualTo(0);
    }
}
```

## Best Practices in Spring Boot

1. **Use @Transactional on service layer methods**, not repository methods
2. **Keep transactions as short as possible**
3. **Handle exceptions appropriately** - RuntimeExceptions cause rollback by default
4. **Use read-only transactions** for query-only operations:
    
    ```java
    @Transactional(readOnly = true)public List<User> getAllUsers() {    return userRepository.findAll();}
    ```
    
5. **Be careful with async methods** - @Async methods don't participate in caller's transaction
6. **Test transaction boundaries** thoroughly

## Common Pitfalls

- @Transactional doesn't work on private methods or self-invocation
- Checked exceptions don't trigger rollback by default
- Mixing different transaction managers incorrectly
- Not understanding that @Transactional creates proxies
- Using transactions in @Configuration classes incorrectly

Spring Boot makes transaction management much easier with its auto-configuration, but understanding the underlying concepts is still crucial for building robust applications.



**TransactionTemplate** is a Spring utility class that simplifies **programmatic transaction management**. Instead of manually managing transactions using `PlatformTransactionManager`, developers can use `TransactionTemplate` to execute business logic within a transaction with less boilerplate code.

### Key Features:
- Provides a convenient way to execute transactional operations.
- Eliminates the need for explicit `begin`, `commit`, and `rollback` calls.
- Uses **callback-based execution**, meaning developers define transactional logic within a lambda or an anonymous class.
- Automatically handles transaction propagation and exception handling.

### Example Usage:
```java
@Service
public class PaymentService {

    @Autowired
    private TransactionTemplate transactionTemplate;
    @Autowired
    private PaymentRepository paymentRepository;

    public PaymentResult processPayment(PaymentRequest request) {
        return transactionTemplate.execute(status -> {
            try {
                // Transactional operation
                Payment payment = new Payment();
                payment.setAmount(request.getAmount());
                payment.setAccountId(request.getAccountId());
                Payment savedPayment = paymentRepository.save(payment);

                // External service call
                ExternalPaymentResult result = callPaymentGateway(request);

                if (!result.isSuccessful()) {
                    // Rollback transaction on failure
                    throw new PaymentException("Payment failed: " + result.getErrorMessage());
                }

                savedPayment.setTransactionId(result.getTransactionId());
                savedPayment.setStatus(PaymentStatus.COMPLETED);
                return new PaymentResult(savedPayment, true);

            } catch (Exception e) {
                // Transaction will be rolled back
                return new PaymentResult(null, false);
            }
        });
    }
}
```
### Benefits:
- **Encapsulation of transaction management** within a single method.
- **Automatic rollback** on exceptions.
- **Reduced code complexity** compared to explicit transaction management using `PlatformTransactionManager`.

Would you like a comparison between `TransactionTemplate` and declarative transaction management (`@Transactional`) in Spring?