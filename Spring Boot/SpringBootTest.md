`@SpringBootTest` is a core testing annotation in Spring Boot that's used for integration testing. It's more comprehensive than `@DataJPATest` as it loads the full application context. Let me explain its key features and usage:

## What is @SpringBootTest?

`@SpringBootTest` creates a fully configured Spring application context for your tests, loading all beans and configurations as defined in your application. This makes it ideal for integration testing where you want to test how different components work together.

## Key features of @SpringBootTest:

1. **Complete context loading**: Loads your entire Spring application context, including all beans, configurations, and properties.
    
2. **Configuration options**: Offers extensive customization through attributes and complementary annotations.
    
3. **Web environment support**: Can test web applications with different web environments (MOCK, RANDOM_PORT, DEFINED_PORT, NONE).
    
4. **Property overrides**: Allows overriding application properties specifically for tests.
    

## Example usage:

```java
@SpringBootTest
public class UserServiceIntegrationTests {

    @Autowired
    private UserService userService;
    
    @Autowired
    private UserRepository userRepository;
    
    @Test
    public void testUserRegistration() {
        // Given
        UserRegistrationRequest request = new UserRegistrationRequest("test@example.com", "password", "Test User");
        
        // When
        User registeredUser = userService.registerUser(request);
        
        // Then
        assertThat(registeredUser).isNotNull();
        assertThat(registeredUser.getEmail()).isEqualTo("test@example.com");
        
        // Verify user is in the database
        Optional<User> savedUser = userRepository.findById(registeredUser.getId());
        assertThat(savedUser).isPresent();
    }
}
```

## Common customizations:

1. **Web environment configuration**:
    
    ```java
    @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
    public class WebLayerTests {
        @Autowired
        private TestRestTemplate restTemplate;
        
        @Test
        public void testEndpoint() {
            ResponseEntity<String> response = restTemplate.getForEntity("/api/users", String.class);
            assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
        }
    }
    ```
    
2. **Overriding properties**:
    
    ```java
    @SpringBootTest(properties = {
        "spring.datasource.url=jdbc:h2:mem:testdb",
        "spring.jpa.hibernate.ddl-auto=create-drop"
    })
    public class ConfigurationTests {
        // test methods
    }
    ```
    
3. **Loading specific configuration classes**:
    
    ```java
    @SpringBootTest(classes = {TestConfig.class, ServiceConfig.class})
    public class SpecificConfigTests {
        // test methods
    }
    ```
    

## @SpringBootTest vs. @DataJPATest:

- **Scope**: `@SpringBootTest` loads the entire application context, while `@DataJPATest` focuses only on the JPA layer.
- **Speed**: `@DataJPATest` tests run faster because they load fewer components.
- **Use case**: Use `@SpringBootTest` for broad integration tests across multiple layers, and `@DataJPATest` for focused repository testing.

`@SpringBootTest` is ideal when you need to test how different parts of your application work together in a full application context, rather than testing components in isolation.