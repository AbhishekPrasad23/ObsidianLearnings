
# REST API Methods (HTTP Verbs)

REST APIs use standard HTTP methods to perform operations on resources. Here are the primary methods:

## 1. **GET**
- **Purpose**: Retrieve a resource or collection of resources
- **Idempotent**: Yes (repeated calls return same result)
- **Safe**: Yes (no server state modification)
- **Example**:
  ```http
  GET /api/users
  GET /api/users/123
  ```

 2. **POST**
- **Purpose**: Create a new resource or submit data
- **Idempotent**: No (repeated calls create multiple resources)
- **Safe**: No
- **Example**:
  ```http
  POST /api/users
  Body: {"name": "John", "email": "john@example.com"}
  ```

## 3. **PUT**
- **Purpose**: Update an existing resource (full replacement)
- **Idempotent**: Yes
- **Safe**: No
- **Example**:
  ```http
  PUT /api/users/123
  Body: {"name": "John Updated", "email": "john.new@example.com"}
  ```

## 4. **PATCH**
- **Purpose**: Partial update of a resource
- **Idempotent**: Depends on implementation
- **Safe**: No
- **Example**:
  ```http
  PATCH /api/users/123
  Body: {"email": "john.updated@example.com"}
  ```

## 5. **DELETE**
- **Purpose**: Remove a resource
- **Idempotent**: Yes
- **Safe**: No
- **Example**:
  ```http
  DELETE /api/users/123
  ```

## Less Common Methods:

### 6. **HEAD**
- Like GET but returns only headers (no body)
- Used to check resource existence or metadata

### 7. **OPTIONS**
- Returns supported HTTP methods for a resource
- Used for CORS preflight requests

### 8. **TRACE**
- Echoes the received request (for debugging)

### 9. **CONNECT**
- Establishes a tunnel to the server (used for HTTPS)

## Best Practices:

1. Use proper HTTP methods for their intended purposes
2. GET requests should never modify server state
3. PUT should be used for complete updates, PATCH for partial updates
4. DELETE operations should be idempotent (multiple calls have same effect as one)
5. Always return appropriate HTTP status codes:
   - 200 (OK) for successful GET/PUT/PATCH/DELETE
   - 201 (Created) for successful POST
   - 204 (No Content) for successful DELETE with no response body

## Example CRUD Mapping:

| Operation | HTTP Method | Endpoint Example     |
|-----------|------------|----------------------|
| Create    | POST       | /api/users           |
| Read      | GET        | /api/users/123       |
| Update    | PUT/PATCH  | /api/users/123       |
| Delete    | DELETE     | /api/users/123       |
| List      | GET        | /api/users           |