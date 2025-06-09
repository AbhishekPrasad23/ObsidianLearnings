
Creational

1.Factory Design Pattern
The Factory Design Pattern is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created. Below is an example of how to implement the Factory Design Pattern in Java.

### Step 1: Define the Product Interface
First, define an interface or an abstract class that represents the product.

```java
// Product interface
interface Product {
    void use();
}
```

### Step 2: Create Concrete Products
Next, create concrete classes that implement the `Product` interface.

```java
// Concrete Product A
class ConcreteProductA implements Product {
    @Override
    public void use() {
        System.out.println("Using ConcreteProductA");
    }
}

// Concrete Product B
class ConcreteProductB implements Product {
    @Override
    public void use() {
        System.out.println("Using ConcreteProductB");
    }
}
```

### Step 3: Create the Factory Class
Now, create a factory class that is responsible for creating instances of the concrete products.

```java
// Factory class
class ProductFactory {
    // Factory method to create products
    public static Product createProduct(String type) {
        if (type.equalsIgnoreCase("A")) {
            return new ConcreteProductA();
        } else if (type.equalsIgnoreCase("B")) {
            return new ConcreteProductB();
        } else {
            throw new IllegalArgumentException("Unknown product type");
        }
    }
}
```

### Step 4: Use the Factory to Create Products
Finally, use the factory to create instances of the products.

```java
// Client code
public class FactoryPatternDemo {
    public static void main(String[] args) {
        // Create Product A
        Product productA = ProductFactory.createProduct("A");
        productA.use();  // Output: Using ConcreteProductA

        // Create Product B
        Product productB = ProductFactory.createProduct("B");
        productB.use();  // Output: Using ConcreteProductB
    }
}
```

### Explanation:
- **Product Interface**: This is the common interface for all products.
- **ConcreteProductA and ConcreteProductB**: These are the concrete implementations of the `Product` interface.
- **ProductFactory**: This class contains a static method `createProduct` that returns an instance of the appropriate product based on the input parameter.
- **Client Code**: The client code uses the `ProductFactory` to create instances of the products without knowing the specific class names.

### Benefits:
- **Decoupling**: The client code is decoupled from the concrete classes. It only interacts with the factory and the product interface.
- **Flexibility**: Adding new products is easy. You just need to create a new concrete product class and update the factory method if necessary.

This is a basic example of the Factory Design Pattern. Depending on your use case, you might need to extend or modify this pattern.



2. Abstract Factory Method

The **Abstract Factory Pattern** is a creational design pattern that provides an interface for creating families of related or dependent objects without specifying their concrete classes. It is an extension of the Factory Method pattern and is useful when you need to create multiple types of objects that belong to a common theme or family.

Below is an example of how to implement the **Abstract Factory Pattern** in Java.

---

### Step 1: Define Abstract Product Interfaces
Define interfaces for the products that belong to a family.

```java
// Abstract Product A
interface Button {
    void render();
}

// Abstract Product B
interface Checkbox {
    void render();
}
```

---

### Step 2: Create Concrete Products
Implement the product interfaces for different families (e.g., Windows and Mac).

```java
// Concrete Product A1: Windows Button
class WindowsButton implements Button {
    @Override
    public void render() {
        System.out.println("Render a button in Windows style");
    }
}

// Concrete Product A2: Mac Button
class MacButton implements Button {
    @Override
    public void render() {
        System.out.println("Render a button in Mac style");
    }
}

// Concrete Product B1: Windows Checkbox
class WindowsCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Render a checkbox in Windows style");
    }
}

// Concrete Product B2: Mac Checkbox
class MacCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Render a checkbox in Mac style");
    }
}
```

---

### Step 3: Define the Abstract Factory Interface
Define an interface for the abstract factory that creates families of related products.

```java
// Abstract Factory
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}
```

---

### Step 4: Create Concrete Factories
Implement the abstract factory for each family (e.g., Windows and Mac).

```java
// Concrete Factory 1: Windows Factory
class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

// Concrete Factory 2: Mac Factory
class MacFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new MacCheckbox();
    }
}
```

---

### Step 5: Use the Abstract Factory in Client Code
The client code uses the abstract factory to create families of related objects without knowing their concrete classes.

```java
// Client Code
public class AbstractFactoryDemo {
    public static void main(String[] args) {
        // Create a Windows GUI
        GUIFactory windowsFactory = new WindowsFactory();
        Button windowsButton = windowsFactory.createButton();
        Checkbox windowsCheckbox = windowsFactory.createCheckbox();

        windowsButton.render();      // Output: Render a button in Windows style
        windowsCheckbox.render();    // Output: Render a checkbox in Windows style

        // Create a Mac GUI
        GUIFactory macFactory = new MacFactory();
        Button macButton = macFactory.createButton();
        Checkbox macCheckbox = macFactory.createCheckbox();

        macButton.render();          // Output: Render a button in Mac style
        macCheckbox.render();        // Output: Render a checkbox in Mac style
    }
}
```

---

### Explanation:
1. **Abstract Products**: `Button` and `Checkbox` are abstract products that define the interface for all concrete products.
2. **Concrete Products**: `WindowsButton`, `MacButton`, `WindowsCheckbox`, and `MacCheckbox` are concrete implementations of the abstract products.
3. **Abstract Factory**: `GUIFactory` is the abstract factory interface that defines methods for creating products.
4. **Concrete Factories**: `WindowsFactory` and `MacFactory` are concrete factories that implement the `GUIFactory` interface and create products for their respective families.
5. **Client Code**: The client code uses the abstract factory to create families of related objects without knowing their concrete classes.

---

### Benefits of Abstract Factory Pattern:
1. **Encapsulation**: The client code is decoupled from the concrete classes of the products.
2. **Consistency**: Ensures that the created objects belong to the same family (e.g., all Windows or all Mac components).
3. **Extensibility**: Adding new families of products is easy by introducing new concrete factories and products.

---

### When to Use:
- When you need to create families of related objects.
- When you want to enforce consistency among products.
- When you want to hide the implementation details of the products from the client code.

This pattern is commonly used in GUI libraries, cross-platform development, and systems that require multiple interchangeable families of objects.


3.Builder Design Pattern

The **Builder Pattern** is a creational design pattern that separates the construction of a complex object from its representation. It allows you to create objects step-by-step, providing flexibility and readability, especially when dealing with objects that have many optional parameters or configurations.

Below is an example of how to implement the **Builder Pattern** in Java.

---

### Step 1: Define the Product Class
Create the class that you want to build. This class may have many attributes, some of which are optional.

```java
// Product Class
class Computer {
    private String CPU;
    private String RAM;
    private String storage;
    private String GPU;

    // Setters for the attributes
    public void setCPU(String CPU) {
        this.CPU = CPU;
    }

    public void setRAM(String RAM) {
        this.RAM = RAM;
    }

    public void setStorage(String storage) {
        this.storage = storage;
    }

    public void setGPU(String GPU) {
        this.GPU = GPU;
    }

    @Override
    public String toString() {
        return "Computer [CPU=" + CPU + ", RAM=" + RAM + ", storage=" + storage + ", GPU=" + GPU + "]";
    }
}
```

---

### Step 2: Define the Builder Interface
Create an interface or abstract class that defines the steps required to build the product.

```java
// Builder Interface
interface ComputerBuilder {
    void buildCPU();
    void buildRAM();
    void buildStorage();
    void buildGPU();
    Computer getComputer();
}
```

---

### Step 3: Create Concrete Builders
Implement the builder interface for different configurations of the product.

```java
// Concrete Builder 1: Gaming Computer
class GamingComputerBuilder implements ComputerBuilder {
    private Computer computer;

    public GamingComputerBuilder() {
        this.computer = new Computer();
    }

    @Override
    public void buildCPU() {
        computer.setCPU("Intel Core i9");
    }

    @Override
    public void buildRAM() {
        computer.setRAM("32GB DDR5");
    }

    @Override
    public void buildStorage() {
        computer.setStorage("1TB SSD");
    }

    @Override
    public void buildGPU() {
        computer.setGPU("NVIDIA RTX 4090");
    }

    @Override
    public Computer getComputer() {
        return this.computer;
    }
}

// Concrete Builder 2: Office Computer
class OfficeComputerBuilder implements ComputerBuilder {
    private Computer computer;

    public OfficeComputerBuilder() {
        this.computer = new Computer();
    }

    @Override
    public void buildCPU() {
        computer.setCPU("Intel Core i5");
    }

    @Override
    public void buildRAM() {
        computer.setRAM("16GB DDR4");
    }

    @Override
    public void buildStorage() {
        computer.setStorage("512GB SSD");
    }

    @Override
    public void buildGPU() {
        computer.setGPU("Integrated Graphics");
    }

    @Override
    public Computer getComputer() {
        return this.computer;
    }
}
```

---

### Step 4: Define the Director
The director class is responsible for managing the construction process. It uses the builder to construct the product step-by-step.

```java
// Director
class ComputerDirector {
    private ComputerBuilder computerBuilder;

    public ComputerDirector(ComputerBuilder computerBuilder) {
        this.computerBuilder = computerBuilder;
    }

    public void constructComputer() {
        computerBuilder.buildCPU();
        computerBuilder.buildRAM();
        computerBuilder.buildStorage();
        computerBuilder.buildGPU();
    }

    public Computer getComputer() {
        return computerBuilder.getComputer();
    }
}
```

---

### Step 5: Use the Builder in Client Code
The client code uses the director and builder to create the product.

```java
// Client Code
public class BuilderPatternDemo {
    public static void main(String[] args) {
        // Build a Gaming Computer
        ComputerBuilder gamingBuilder = new GamingComputerBuilder();
        ComputerDirector gamingDirector = new ComputerDirector(gamingBuilder);
        gamingDirector.constructComputer();
        Computer gamingComputer = gamingDirector.getComputer();
        System.out.println("Gaming Computer: " + gamingComputer);

        // Build an Office Computer
        ComputerBuilder officeBuilder = new OfficeComputerBuilder();
        ComputerDirector officeDirector = new ComputerDirector(officeBuilder);
        officeDirector.constructComputer();
        Computer officeComputer = officeDirector.getComputer();
        System.out.println("Office Computer: " + officeComputer);
    }
}
```

---

### Output:
```
Gaming Computer: Computer [CPU=Intel Core i9, RAM=32GB DDR5, storage=1TB SSD, GPU=NVIDIA RTX 4090]
Office Computer: Computer [CPU=Intel Core i5, RAM=16GB DDR4, storage=512GB SSD, GPU=Integrated Graphics]
```

---

### Explanation:
1. **Product Class**: The `Computer` class represents the complex object to be built.
2. **Builder Interface**: The `ComputerBuilder` interface defines the steps required to build the product.
3. **Concrete Builders**: The `GamingComputerBuilder` and `OfficeComputerBuilder` classes implement the builder interface for specific configurations.
4. **Director**: The `ComputerDirector` class manages the construction process using the builder.
5. **Client Code**: The client code uses the director and builder to create the product.

---

### Benefits of Builder Pattern:
1. **Flexibility**: Allows you to create different configurations of an object using the same construction process.
2. **Readability**: Makes the code more readable, especially when dealing with objects that have many attributes.
3. **Separation of Concerns**: Separates the construction logic from the representation of the object.

---

### When to Use:
- When you need to create complex objects with many optional parameters.
- When you want to avoid using a constructor with many parameters (telescoping constructor anti-pattern).
- When you want to ensure immutability of the object after construction.

This pattern is commonly used in libraries like `StringBuilder`, `DocumentBuilder`, and frameworks like Lombok's `@Builder` annotation.




Prototype design pattern is one of the Creational Design pattern, so it provides a mechanism of object creation.

## [Prototype Design Pattern](https://www.digitalocean.com/community/tutorials/prototype-design-pattern-in-java#prototype-design-pattern)[](https://www.digitalocean.com/community/tutorials/prototype-design-pattern-in-java#prototype-design-pattern)

[![prototype design pattern](https://journaldev.nyc3.cdn.digitaloceanspaces.com/2013/06/prototype-design-pattern.jpg)](https://journaldev.nyc3.cdn.digitaloceanspaces.com/2013/06/prototype-design-pattern.jpg)Prototype design pattern is used when the Object creation is a costly affair and requires a lot of time and resources and you have a similar object already existing. Prototype pattern provides a mechanism to copy the original object to a new object and then modify it according to our needs. Prototype design pattern uses java cloning to copy the object.

### [Prototype Design Pattern Example](https://www.digitalocean.com/community/tutorials/prototype-design-pattern-in-java#prototype-design-pattern-example)[](https://www.digitalocean.com/community/tutorials/prototype-design-pattern-in-java#prototype-design-pattern-example)

It would be easy to understand prototype design pattern with an example. Suppose we have an Object that loads data from database. Now we need to modify this data in our program multiple times, so it’s not a good idea to create the Object using `new` keyword and load all the data again from database. The better approach would be to clone the existing object into a new object and then do the data manipulation. Prototype design pattern mandates that the Object which you are copying should provide the copying feature. It should not be done by any other class. However whether to use shallow or deep copy of the Object properties depends on the requirements and its a design decision. Here is a sample program showing Prototype design pattern example in java. `Employees.java`

```
package com.journaldev.design.prototype;

import java.util.ArrayList;
import java.util.List;

public class Employees implements Cloneable{

	private List<String> empList;
	
	public Employees(){
		empList = new ArrayList<String>();
	}
	
	public Employees(List<String> list){
		this.empList=list;
	}
	public void loadData(){
		//read all employees from database and put into the list
		empList.add("Pankaj");
		empList.add("Raj");
		empList.add("David");
		empList.add("Lisa");
	}
	
	public List<String> getEmpList() {
		return empList;
	}

	@Override
	public Object clone() throws CloneNotSupportedException{
			List<String> temp = new ArrayList<String>();
			for(String s : this.getEmpList()){
				temp.add(s);
			}
			return new Employees(temp);
	}
	
}
```

Notice that the `clone` method is overridden to provide a deep copy of the employees list. Here is the prototype design pattern example test program that will show the benefit of prototype pattern. `PrototypePatternTest.java`

```
package com.journaldev.design.test;

import java.util.List;

import com.journaldev.design.prototype.Employees;

public class PrototypePatternTest {

	public static void main(String[] args) throws CloneNotSupportedException {
		Employees emps = new Employees();
		emps.loadData();
		
		//Use the clone method to get the Employee object
		Employees empsNew = (Employees) emps.clone();
		Employees empsNew1 = (Employees) emps.clone();
		List<String> list = empsNew.getEmpList();
		list.add("John");
		List<String> list1 = empsNew1.getEmpList();
		list1.remove("Pankaj");
		
		System.out.println("emps List: "+emps.getEmpList());
		System.out.println("empsNew List: "+list);
		System.out.println("empsNew1 List: "+list1);
	}

}
```

Output of the above prototype design pattern example program is:

```
emps List: [Pankaj, Raj, David, Lisa]
empsNew List: [Pankaj, Raj, David, Lisa, John]
empsNew1 List: [Raj, David, Lisa]
```

If the object cloning was not provided, we will have to make database call to fetch the employee list every time. Then do the manipulations that would have been resource and time consuming. That’s all for prototype design pattern in java.