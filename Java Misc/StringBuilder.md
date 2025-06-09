`StringBuilder` in Java is a mutable sequence of characters, which means it can be modified after it is created. It is more efficient than using `String` for concatenating multiple strings, especially in loops, because it avoids the overhead of creating multiple `String` objects. Below are some of the key methods provided by the `StringBuilder` class:

---

### **Core Methods**

1. **`append()`**
   - Appends the specified value to the end of the sequence.
   - Overloaded for various data types (`String`, `int`, `char`, `boolean`, etc.).
   - Example:
     ```java
     StringBuilder sb = new StringBuilder("Hello");
     sb.append(" World"); // "Hello World"
     ```

2. **`insert()`**
   - Inserts the specified value at the given position.
   - Overloaded for various data types.
   - Example:
     ```java
     StringBuilder sb = new StringBuilder("Hello");
     sb.insert(5, " World"); // "Hello World"
     ```

3. **`delete()`**
   - Removes characters from the specified start index to the end index (exclusive).
   - Example:
     ```java
     StringBuilder sb = new StringBuilder("Hello World");
     sb.delete(5, 11); // "Hello"
     ```

4. **`deleteCharAt()`**
   - Removes the character at the specified index.
   - Example:
     ```java
     StringBuilder sb = new StringBuilder("Hello");
     sb.deleteCharAt(1); // "Hllo"
     ```

5. **`replace()`**
   - Replaces the characters in a substring of the sequence with the specified string.
   - Example:
     ```java
     StringBuilder sb = new StringBuilder("Hello World");
     sb.replace(6, 11, "Java"); // "Hello Java"
     ```

6. **`reverse()`**
   - Reverses the sequence of characters.
   - Example:
     ```java
     StringBuilder sb = new StringBuilder("Hello");
     sb.reverse(); // "olleH"
     ```

7. **`toString()`**
   - Converts the `StringBuilder` to a `String`.
   - Example:
     ```java
     StringBuilder sb = new StringBuilder("Hello");
     String str = sb.toString(); // "Hello"
     ```

8. **`length()`**
   - Returns the length (number of characters) of the sequence.
   - Example:
     ```java
     StringBuilder sb = new StringBuilder("Hello");
     int len = sb.length(); // 5
     ```

9. **`capacity()`**
   - Returns the current capacity of the `StringBuilder` (the amount of storage available for newly inserted characters).
   - Example:
     ```java
     StringBuilder sb = new StringBuilder();
     int cap = sb.capacity(); // Default capacity (usually 16)
     ```

10. **`setLength()`**
    - Sets the length of the character sequence.
    - If the new length is greater than the current length, null characters (`\u0000`) are appended.
    - If the new length is less than the current length, the sequence is truncated.
    - Example:
      ```java
      StringBuilder sb = new StringBuilder("Hello");
      sb.setLength(3); // "Hel"
      ```

11. **`charAt()`**
    - Returns the character at the specified index.
    - Example:
      ```java
      StringBuilder sb = new StringBuilder("Hello");
      char ch = sb.charAt(1); // 'e'
      ```

12. **`setCharAt()`**
    - Sets the character at the specified index.
    - Example:
      ```java
      StringBuilder sb = new StringBuilder("Hello");
      sb.setCharAt(1, 'a'); // "Hallo"
      ```

13. **`substring()`**
    - Returns a new `String` that is a substring of the sequence.
    - Overloaded to take a start index or a start and end index.
    - Example:
      ```java
      StringBuilder sb = new StringBuilder("Hello World");
      String sub = sb.substring(6); // "World"
      ```

14. **`indexOf()`**
    - Returns the index of the first occurrence of the specified substring.
    - Example:
      ```java
      StringBuilder sb = new StringBuilder("Hello World");
      int index = sb.indexOf("World"); // 6
      ```

15. **`lastIndexOf()`**
    - Returns the index of the last occurrence of the specified substring.
    - Example:
      ```java
      StringBuilder sb = new StringBuilder("Hello World");
      int index = sb.lastIndexOf("o"); // 7
      ```

16. **`ensureCapacity()`**
    - Ensures that the capacity is at least equal to the specified minimum.
    - Example:
      ```java
      StringBuilder sb = new StringBuilder();
      sb.ensureCapacity(100); // Ensures capacity of at least 100
      ```

17. **`trimToSize()`**
    - Reduces the storage used for the character sequence to match its current length.
    - Example:
      ```java
      StringBuilder sb = new StringBuilder("Hello");
      sb.trimToSize(); // Reduces capacity to match length
      ```

---

### **Example Usage**
```java
public class StringBuilderExample {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello");
        sb.append(" World"); // "Hello World"
        sb.insert(5, ","); // "Hello, World"
        sb.replace(7, 12, "Java"); // "Hello, Java"
        sb.reverse(); // "avaJ ,olleH"
        System.out.println(sb.toString()); // Output: avaJ ,olleH
    }
}
```

---

### **Key Points**
- `StringBuilder` is not thread-safe. For thread-safe operations, use `StringBuffer`.
- It is more efficient than `String` for frequent modifications.
- The default capacity is 16 characters, but it grows dynamically as needed.

Let me know if you need further clarification!