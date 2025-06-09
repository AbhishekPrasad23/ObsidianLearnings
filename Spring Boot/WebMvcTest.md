`@WebMvcTest` is a specialized testing annotation in Spring Boot focused on testing Spring MVC controllers in isolation. It's part of Spring Boot's slice testing support, designed to load only the components needed for testing web controllers. Let me explain its features and usage:

## What is @WebMvcTest?

`@WebMvcTest` loads only the web layer of your application, including controllers, `@ControllerAdvice`, `WebMvcConfigurer`, and `HandlerMethodArgumentResolver`. It doesn't load the full application context like `@SpringBootTest` does, making tests faster and more focused.

## Key features of @WebMvcTest:

1. **Limited context**: Only loads the web layer, without loading service or repository layers.
    
2. **Auto-configuration**: Automatically configures:
    
    - MockMvc for simulating HTTP requests
    - Web security (if present)
    - Form validation
    - JSON binding
3. **Targeted controller testing**: Can be configured to load specific controllers rather than all.
    
4. **Mock requirement**: Since service layer is not loaded, you typically need to use `@MockBean` to mock dependencies of your controllers.
    

## Example usage:

```java
@WebMvcTest(UserController.class)
public class UserControllerTests {

    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    private UserService userService;
    
    @Test
    public void testGetUser() throws Exception {
        User mockUser = new User(1L, "john.doe@example.com", "John Doe");
        when(userService.getUserById(1L)).thenReturn(mockUser);
        
        mockMvc.perform(get("/api/users/1")
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.id").value(1))
                .andExpect(jsonPath("$.email").value("john.doe@example.com"))
                .andExpect(jsonPath("$.name").value("John Doe"));
    }
    
    @Test
    public void testCreateUser() throws Exception {
        UserDto userDto = new UserDto("jane.doe@example.com", "Jane Doe");
        User createdUser = new User(2L, "jane.doe@example.com", "Jane Doe");
        
        when(userService.createUser(any(UserDto.class))).thenReturn(createdUser);
        
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"email\":\"jane.doe@example.com\",\"name\":\"Jane Doe\"}"))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.id").value(2));
    }
}
```

## Common customizations:

1. **Testing specific controllers**:
    
    ```java
    @WebMvcTest({UserController.class, AuthController.class})
    public class MultipleControllerTests {
        // test methods
    }
    ```
    
2. **Including security configuration**:
    
    ```java
    @WebMvcTest(UserController.class)
    @Import(SecurityConfig.class)
    public class SecuredControllerTests {
        @Autowired
        private MockMvc mockMvc;
        
        @MockBean
        private UserDetailsService userDetailsService;
        
        @Test
        @WithMockUser(roles = "ADMIN")
        public void testSecuredEndpoint() throws Exception {
            // test with mock security context
        }
    }
    ```
    
3. **Testing with filters**:
    
    ```java
    @WebMvcTest(UserController.class)
    public class ControllerWithFiltersTests {
        @Autowired
        private MockMvc mockMvc;
        
        @MockBean
        private JwtAuthenticationFilter jwtFilter;
        
        @Test
        public void testEndpointWithFilter() throws Exception {
            mockMvc.perform(get("/api/users")
                    .header("Authorization", "Bearer mock-token"))
                    .andExpect(status().isOk());
        }
    }
    ```
    

## When to use @WebMvcTest:

- When you want to test MVC controllers in isolation
- When you need to verify request mappings, validations, and response handling
- When testing how controllers interact with request/response objects
- For focused testing of controller-level exception handling
- To test security rules applied at the controller level

`@WebMvcTest` is ideal for testing the web layer of your application without the overhead of loading the entire application context, making your controller tests fast and focused.