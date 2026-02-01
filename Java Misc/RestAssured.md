
# Response & Request Spec in RestAssured

## Overview

RestAssured is a popular Java library for testing RESTful APIs. The ResponseSpec and RequestSpec interfaces help you create reusable specifications for your API tests, making your test code more maintainable and DRY (Don't Repeat Yourself).

## RequestSpec

**Purpose**: Encapsulates common request configurations that can be reused across multiple tests.

**Use Cases**:
- Common base URL
- Authentication headers
- Content type
- Request logging
- Common query parameters
- Cookies

**Implementation**:
```java
RequestSpecification requestSpec = given()
    .baseUri("https://api.example.com")
    .contentType(ContentType.JSON)
    .auth().oauth2(accessToken)
    .log().all();
```

## ResponseSpec

**Purpose**: Encapsulates common response assertions that can be reused across multiple tests.

**Use Cases**:
- Status code verification
- Response time validation
- Content type verification
- Common response body assertions
- Response logging

**Implementation**:
```java
ResponseSpecification responseSpec = expect()
    .statusCode(200)
    .contentType(ContentType.JSON)
    .time(lessThan(2000L))
    .log().all();
```

## Using Specs Together

```java
@Test
public void getUserTest() {
    given()
        .spec(requestSpec)
        .pathParam("id", 123)
    .when()
        .get("/users/{id}")
    .then()
        .spec(responseSpec)
        .body("id", equalTo(123))
        .body("name", notNullValue());
}
```

## Why Create an Abstraction Layer?

1. **Code Reusability**: Avoid duplicating common configurations across tests
2. **Maintainability**: Change configurations in one place rather than many test files
3. **Readability**: Tests become more focused on specific behavior rather than setup
4. **Consistency**: Ensures all tests follow the same standards
5. **Separation of Concerns**: Technical details are separated from test logic

## Abstraction Layer Implementation

A common pattern is to create a base test class or utility class that provides these specifications:

```java
public class ApiTestBase {
    protected static RequestSpecification requestSpec;
    protected static ResponseSpecification responseSpec;
    
    @BeforeAll
    public static void setup() {
        requestSpec = given()
            .baseUri(Config.getBaseUrl())
            .contentType(ContentType.JSON)
            .auth().oauth2(getAuthToken());
            
        responseSpec = expect()
            .contentType(ContentType.JSON)
            .time(lessThan(Config.getMaxResponseTime()));
    }
    
    private static String getAuthToken() {
        // token retrieval logic
    }
}
```

Then your test classes can extend this base class:

```java
public class UserTests extends ApiTestBase {
    @Test
    public void testGetUser() {
        given()
            .spec(requestSpec)
            .pathParam("id", 1)
        .when()
            .get("/users/{id}")
        .then()
            .spec(responseSpec)
            .statusCode(200)
            .body("id", equalTo(1));
    }
}
```

This abstraction makes your tests cleaner and more maintainable while ensuring consistency across your test suite.