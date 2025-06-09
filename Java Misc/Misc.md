# The 4 Pillars of Object-Oriented Programming (OOP) - Brief Overview

## 1. **Encapsulation**
- Bundling data (attributes) and methods (functions) that operate on the data into a single unit (class)
- Restricts direct access to some components through access modifiers (private, protected, public)
- Example: 
  ```java
  class BankAccount {
      private double balance;  // Hidden data
      
      public void deposit(double amount) {  // Controlled access
          if(amount > 0) balance += amount;
      }
  }
  ```

## 2. **Abstraction**
- Hiding complex implementation details and showing only essential features
- Achieved through abstract classes and interfaces
- Example:
  ```java
  abstract class Vehicle {
      abstract void start();  // What to do, not how
  }
  ```

## 3. **Inheritance**
- Creating new classes (child/derived) from existing ones (parent/base)
- Promotes code reusability and establishes "is-a" relationships
- Example:
  ```java
  class Car extends Vehicle {  // Car inherits from Vehicle
      void start() { System.out.println("Car starts"); }
  }
  ```

## 4. **Polymorphism**
- "Many forms" - ability to present the same interface for different underlying forms
- Two types:
  - **Compile-time** (Method overloading)
    ```java
    void print(int i) { ... }
    void print(String s) { ... }
    ```
  - **Runtime** (Method overriding)
    ```java
    Animal a = new Dog();
    a.sound();  // Calls Dog's implementation
    ```

These pillars work together to create flexible, maintainable, and scalable software systems.
## Key Rules for Abstract Methods in Interfaces

1. **Implicit Modifiers**:
    
    - All methods are `public abstract` by default
        
    - All fields are `public static final` by default
        
2. **Implementation Requirements**:
    
    - Concrete classes must implement all abstract methods
        
    - Or declare themselves abstract
        
3. **Multiple Inheritance**:
    
    - A class can implement multiple interfaces
        
    - Must implement all abstract methods from all interfaces