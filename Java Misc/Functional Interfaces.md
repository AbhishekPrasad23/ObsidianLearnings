# Functional Interfaces in Java

Functional interfaces are a key concept in Java that enable functional programming features, especially since Java 8. Here's a comprehensive overview:

## What is a Functional Interface?

A functional interface is an interface that contains **exactly one abstract method** (SAM - Single Abstract Method). They can have any number of default or static methods but only one abstract method.

```java
@FunctionalInterface
interface MyFunctionalInterface {
    void execute();  // single abstract method
    
    default void defaultMethod() {
        System.out.println("Default method");
    }
    
    static void staticMethod() {
        System.out.println("Static method");
    }
}
```

## Built-in Functional Interfaces in Java

Java provides several functional interfaces in the `java.util.function` package:

### Core Functional Interfaces:

1. **`Predicate<T>`** - Tests a condition
   ```java
   Predicate<String> isLong = s -> s.length() > 10;
   ```

2. **`Function<T,R>`** - Takes input of type T and returns output of type R
   ```java
   Function<String, Integer> lengthMapper = String::length;
   ```

3. **`Consumer<T>`** - Accepts input but returns nothing
   ```java
   Consumer<String> printer = System.out::println;
   ```

4. **`Supplier<T>`** - Provides output without input
   ```java
   Supplier<Double> randomSupplier = Math::random;
   ```

### Specialized Variants:

- **`UnaryOperator<T>`** - Function where input and output types are same
- **`BinaryOperator<T>`** - Takes two inputs of same type, returns same type
- Primitive variants like `IntPredicate`, `LongFunction`, `DoubleConsumer` etc.

## Using Functional Interfaces

### 1. Lambda Expressions
```java
Runnable r = () -> System.out.println("Running!");
```

### 2. Method References
```java
Function<String, Integer> lengthMapper = String::length;
```

### 3. Constructor References
```java
Supplier<List<String>> listSupplier = ArrayList::new;
```

## Creating Custom Functional Interfaces

```java
@FunctionalInterface
interface StringProcessor {
    String process(String input);
    
    default StringProcessor andThen(StringProcessor after) {
        return input -> after.process(this.process(input));
    }
}

// Usage
StringProcessor upperCase = String::toUpperCase;
StringProcessor trim = String::trim;
StringProcessor pipeline = upperCase.andThen(trim);
```

## Key Points

- The `@FunctionalInterface` annotation is optional but recommended
- Functional interfaces enable lambda expressions in Java
- They are heavily used in the Streams API
- Java maintains backward compatibility - existing single-method interfaces like `Runnable` became functional interfaces

Functional interfaces are fundamental to Java's functional programming capabilities, making code more concise and expressive while maintaining type safety.


# Java Built-in Functional Interfaces - Detailed Guide with Implementations

Java provides a rich set of built-in functional interfaces in the `java.util.function` package. Here's a comprehensive explanation of each major category with implementation examples:

## 1. Core Functional Interfaces

### Predicate<T>
**Purpose:** Tests a condition and returns boolean
```java
Predicate<String> isLong = s -> s.length() > 5;
System.out.println(isLong.test("Hello"));  // true
System.out.println(isLong.test("Hi"));     // false

// Methods: and(), or(), negate(), isEqual()
Predicate<String> containsA = s -> s.contains("a");
Predicate<String> isLongAndContainsA = isLong.and(containsA);
```

### Function<T,R>
**Purpose:** Transforms input of type T to output of type R
```java
Function<String, Integer> lengthFunction = String::length;
System.out.println(lengthFunction.apply("Java"));  // 4

// Methods: andThen(), compose()
Function<Integer, Integer> square = x -> x * x;
Function<Integer, Integer> plusOne = x -> x + 1;

Function<Integer, Integer> squareThenAdd = square.andThen(plusOne);
System.out.println(squareThenAdd.apply(3));  // 10

Function<Integer, Integer> addThenSquare = square.compose(plusOne);
System.out.println(addThenSquare.apply(3));  // 16
```

### Consumer<T>
**Purpose:** Performs operation on input without returning result
```java
Consumer<String> printUpperCase = s -> System.out.println(s.toUpperCase());
printUpperCase.accept("hello");  // HELLO

// Method: andThen()
Consumer<String> printLength = s -> System.out.println(s.length());
Consumer<String> printBoth = printUpperCase.andThen(printLength);
printBoth.accept("Java");  // JAVA then 4
```

### Supplier<T>
**Purpose:** Provides values without taking input
```java
Supplier<Double> randomSupplier = Math::random;
System.out.println(randomSupplier.get());  // e.g., 0.123456

Supplier<LocalDate> todaySupplier = LocalDate::now;
System.out.println(todaySupplier.get());  // current date
```

## 2. Specialized Core Interfaces

### UnaryOperator<T> (extends Function<T,T>)
**Purpose:** Function where input and output types are same
```java
UnaryOperator<String> toUpper = String::toUpperCase;
System.out.println(toUpper.apply("hello"));  // HELLO

UnaryOperator<Integer> square = x -> x * x;
System.out.println(square.apply(5));  // 25
```

### BinaryOperator<T> (extends BiFunction<T,T,T>)
**Purpose:** Operates on two inputs of same type, returns same type
```java
BinaryOperator<Integer> add = (a, b) -> a + b;
System.out.println(add.apply(3, 5));  // 8

BinaryOperator<String> concat = String::concat;
System.out.println(concat.apply("Hello ", "World"));  // Hello World
```

## 3. Two-Arity (Bi) Functional Interfaces

### BiPredicate<T,U>
**Purpose:** Takes two arguments, returns boolean
```java
BiPredicate<String, Integer> isLengthEqual = (s, len) -> s.length() == len;
System.out.println(isLengthEqual.test("Java", 4));  // true
```

### BiFunction<T,U,R>
**Purpose:** Takes two arguments, returns result
```java
BiFunction<String, String, Integer> totalLength = 
    (s1, s2) -> s1.length() + s2.length();
System.out.println(totalLength.apply("Hi", "There"));  // 7
```

### BiConsumer<T,U>
**Purpose:** Takes two arguments, returns nothing
```java
BiConsumer<String, Integer> printRepeat = 
    (s, n) -> System.out.println(s.repeat(n));
printRepeat.accept("Hi", 3);  // HiHiHi
```

## 4. Primitive Type Specializations

### For Predicates
```java
IntPredicate isEven = n -> n % 2 == 0;
System.out.println(isEven.test(4));  // true

DoublePredicate isPositive = d -> d > 0;
System.out.println(isPositive.test(-1.5));  // false
```

### For Functions
```java
IntFunction<String> intToString = Integer::toString;
System.out.println(intToString.apply(123));  // "123"

ToIntFunction<String> stringToInt = Integer::parseInt;
System.out.println(stringToInt.applyAsInt("456"));  // 456
```

### For Consumers
```java
IntConsumer printSquare = n -> System.out.println(n * n);
printSquare.accept(5);  // 25

ObjIntConsumer<String> printRepeat = 
    (s, n) -> System.out.println(s.repeat(n));
printRepeat.accept("A", 5);  // AAAAA
```

## 5. Special Cases

### BooleanSupplier
```java
BooleanSupplier isMorning = () -> LocalTime.now().getHour() < 12;
System.out.println(isMorning.getAsBoolean());
```

### Comparator<T> (Functional Interface since Java 8)
```java
Comparator<String> lengthComparator = 
    (s1, s2) -> s1.length() - s2.length();
List<String> words = Arrays.asList("apple", "banana", "pear");
words.sort(lengthComparator);  // [pear, apple, banana]
```

## Practical Usage Examples

### 1. Filtering with Predicate
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "Dave");
Predicate<String> startsWithA = name -> name.startsWith("A");
names.stream().filter(startsWithA).forEach(System.out::println);  // Alice
```

### 2. Mapping with Function
```java
List<String> words = Arrays.asList("hello", "world");
Function<String, String> capitalize = s -> s.substring(0,1).toUpperCase() + s.substring(1);
words.stream().map(capitalize).forEach(System.out::println);  // Hello World
```

### 3. Combining Functions
```java
Function<Integer, Integer> timesTwo = x -> x * 2;
Function<Integer, Integer> minusOne = x -> x - 1;
Function<Integer, Integer> pipeline = timesTwo.andThen(minusOne);
System.out.println(pipeline.apply(10));  // 19
```

These functional interfaces form the foundation of Java's functional programming capabilities, especially when working with streams and lambda expressions. Each serves specific purposes and their combinations enable powerful, concise data processing patterns.