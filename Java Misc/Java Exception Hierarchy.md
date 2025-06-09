# Java Exception Hierarchy

The Java exception hierarchy is a structured classification of error and exception types. Here's the complete hierarchy with examples:

## The Root: `Throwable`
```
java.lang.Throwable (implements Serializable)
├── java.lang.Error
│   ├── java.lang.VirtualMachineError
│   │   ├── java.lang.OutOfMemoryError
│   │   ├── java.lang.StackOverflowError
│   │   └── java.lang.InternalError
│   ├── java.lang.LinkageError
│   │   ├── java.lang.NoClassDefFoundError
│   │   └── java.lang.UnsatisfiedLinkError
│   └── java.lang.AssertionError
└── java.lang.Exception
    ├── java.lang.RuntimeException (Unchecked)
    │   ├── java.lang.NullPointerException
    │   ├── java.lang.ArrayIndexOutOfBoundsException
    │   ├── java.lang.IllegalArgumentException
    │   │   └── java.lang.NumberFormatException
    │   ├── java.lang.IllegalStateException
    │   └── java.lang.ArithmeticException
    └── [Checked Exceptions]
        ├── java.io.IOException
        │   ├── java.io.FileNotFoundException
        │   └── java.io.EOFException
        ├── java.sql.SQLException
        ├── java.lang.InterruptedException
        └── java.lang.CloneNotSupportedException
```

## Key Categories

### 1. **Checked Exceptions**
- Must be declared or handled (compile-time enforcement)
- Subclasses of `Exception` (but not `RuntimeException`)
- Examples:
  ```java
  // File operations
  try {
      FileReader file = new FileReader("nonexistent.txt");
  } catch (FileNotFoundException e) {
      System.out.println("File not found");
  }
  
  // Database operations
  try {
      Connection conn = DriverManager.getConnection(url);
  } catch (SQLException e) {
      e.printStackTrace();
  }
  ```

### 2. **Unchecked Exceptions (RuntimeExceptions)**
- Don't need to be declared or caught
- Subclasses of `RuntimeException`
- Examples:
  ```java
  // NullPointerException
  String str = null;
  System.out.println(str.length()); // Throws NPE
  
  // ArrayIndexOutOfBoundsException
  int[] arr = new int[5];
  System.out.println(arr[10]); // Throws AIOOBE
  
  // ArithmeticException
  int x = 5 / 0; // Throws ArithmeticException
  ```

### 3. **Errors**
- Serious problems that applications shouldn't try to catch
- Subclasses of `Error`
- Examples:
  ```java
  // OutOfMemoryError
  List<Object> infiniteList = new ArrayList<>();
  while (true) {
      infiniteList.add(new Object());
  }
  
  // StackOverflowError
  public static void recursiveMethod() {
      recursiveMethod(); // Infinite recursion
  }
  ```

## Custom Exceptions

You can create your own exceptions by extending either `Exception` (checked) or `RuntimeException` (unchecked):

```java
// Checked custom exception
public class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String message) {
        super(message);
    }
}

// Unchecked custom exception
public class InvalidUserException extends RuntimeException {
    public InvalidUserException(String message) {
        super(message);
    }
}
```

## Exception Handling Best Practices

1. **Specific before general**: Catch more specific exceptions first
   ```java
   try {
       // code
   } catch (FileNotFoundException e) {
       // handle file not found
   } catch (IOException e) {
       // handle other IO issues
   }
   ```

2. **Don't swallow exceptions**: At least log them
   ```java
   catch (SQLException e) {
       logger.error("Database error", e); // Good
       // e.printStackTrace(); // Better than nothing
       // empty catch block // BAD!
   }
   ```

3. **Use try-with-resources** for auto-closable objects
   ```java
   try (FileInputStream fis = new FileInputStream("file.txt")) {
       // use resource
   } // automatically closed
   ```

4. **Throw early, catch late**: Validate inputs early, handle exceptions at appropriate levels

5. **Document exceptions** with `@throws` in JavaDoc for checked exceptions your method throws