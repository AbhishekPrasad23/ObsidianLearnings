

# Adapter Design Pattern in Java

The **Adapter Design Pattern** is a structural pattern that allows incompatible interfaces to work together. It acts as a bridge between two incompatible interfaces by converting the interface of one class into another interface that clients expect.

## Real-World Analogy

Think of a power adapter - when you travel to another country, you might need an adapter to plug your device into a foreign power outlet. The adapter makes the incompatible outlet compatible with your device plug.

## When to Use Adapter Pattern

1. When you want to use an existing class, but its interface doesn't match what you need
2. When you want to create a reusable class that cooperates with unrelated classes with incompatible interfaces
3. When you need to use several existing subclasses, but it's impractical to adapt their interface by subclassing each one

## Implementation Approaches

There are two main ways to implement the Adapter pattern:

### 1. Class Adapter (using inheritance)
```java
// Target interface (expected by client)
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Adaptee (incompatible interface)
class AdvancedMediaPlayer {
    void playVlc(String fileName) {
        System.out.println("Playing vlc file: " + fileName);
    }
    
    void playMp4(String fileName) {
        System.out.println("Playing mp4 file: " + fileName);
    }
}

// Adapter (inherits from Adaptee)
class MediaAdapter extends AdvancedMediaPlayer implements MediaPlayer {
    @Override
    public void play(String audioType, String fileName) {
        if(audioType.equalsIgnoreCase("vlc")) {
            playVlc(fileName);
        } else if(audioType.equalsIgnoreCase("mp4")) {
            playMp4(fileName);
        }
    }
}
```

### 2. Object Adapter (using composition - more common)
```java
// Target interface
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Adaptee
class AdvancedMediaPlayer {
    void playVlc(String fileName) {
        System.out.println("Playing vlc file: " + fileName);
    }
    
    void playMp4(String fileName) {
        System.out.println("Playing mp4 file: " + fileName);
    }
}

// Adapter (contains reference to Adaptee)
class MediaAdapter implements MediaPlayer {
    private AdvancedMediaPlayer advancedMusicPlayer;
    
    public MediaAdapter() {
        this.advancedMusicPlayer = new AdvancedMediaPlayer();
    }
    
    @Override
    public void play(String audioType, String fileName) {
        if(audioType.equalsIgnoreCase("vlc")) {
            advancedMusicPlayer.playVlc(fileName);
        } else if(audioType.equalsIgnoreCase("mp4")) {
            advancedMusicPlayer.playMp4(fileName);
        }
    }
}
```

## Client Code Example

```java
public class AudioPlayer implements MediaPlayer {
    private MediaAdapter mediaAdapter;
    
    @Override
    public void play(String audioType, String fileName) {
        // Built-in support for mp3
        if(audioType.equalsIgnoreCase("mp3")) {
            System.out.println("Playing mp3 file: " + fileName);
        }
        // MediaAdapter provides support for other formats
        else if(audioType.equalsIgnoreCase("vlc") || audioType.equalsIgnoreCase("mp4")) {
            mediaAdapter = new MediaAdapter();
            mediaAdapter.play(audioType, fileName);
        }
        else {
            System.out.println("Invalid media type");
        }
    }
}

// Usage
public class AdapterDemo {
    public static void main(String[] args) {
        AudioPlayer audioPlayer = new AudioPlayer();
        
        audioPlayer.play("mp3", "song.mp3");
        audioPlayer.play("mp4", "movie.mp4");
        audioPlayer.play("vlc", "video.vlc");
        audioPlayer.play("avi", "movie.avi");
    }
}
```

## Key Components

1. **Target Interface**: The interface that the client expects to work with
2. **Adaptee**: The existing class that needs to be adapted
3. **Adapter**: The class that implements the target interface and translates calls to the adaptee

## Advantages

- Allows reuse of existing functionality with new interfaces
- Promotes loose coupling between components
- More flexible than subclassing for interface adaptation

## Disadvantages

- Adds some complexity to the code
- In some cases, might lead to performance overhead

The Adapter pattern is particularly useful when integrating legacy code or third-party libraries into your system when you can't modify their source code but need them to work with your existing interfaces.




# Composite Design Pattern in Java

The Composite design pattern is a structural pattern that allows you to compose objects into tree structures to represent part-whole hierarchies. It lets clients treat individual objects and compositions of objects uniformly.

## When to Use the Composite Pattern

- When you need to represent part-whole hierarchies of objects
- When you want clients to be able to ignore the difference between compositions of objects and individual objects
- When the structure can have any level of complexity and is dynamic

## Key Components

1. **Component** - The interface/abstract class for all objects in the composition
2. **Leaf** - Represents leaf objects in the composition (implements all Component methods)
3. **Composite** - Stores child components and implements child-related operations in the Component interface

## Java Implementation Example

```java
import java.util.ArrayList;
import java.util.List;

// Component interface
interface Employee {
    void showEmployeeDetails();
}

// Leaf class
class Developer implements Employee {
    private String name;
    private String position;
    
    public Developer(String name, String position) {
        this.name = name;
        this.position = position;
    }
    
    @Override
    public void showEmployeeDetails() {
        System.out.println("Developer: " + name + ", Position: " + position);
    }
}

// Leaf class
class Manager implements Employee {
    private String name;
    private String position;
    
    public Manager(String name, String position) {
        this.name = name;
        this.position = position;
    }
    
    @Override
    public void showEmployeeDetails() {
        System.out.println("Manager: " + name + ", Position: " + position);
    }
}

// Composite class
class CompanyDirectory implements Employee {
    private List<Employee> employeeList = new ArrayList<>();
    
    @Override
    public void showEmployeeDetails() {
        for(Employee emp : employeeList) {
            emp.showEmployeeDetails();
        }
    }
    
    public void addEmployee(Employee emp) {
        employeeList.add(emp);
    }
    
    public void removeEmployee(Employee emp) {
        employeeList.remove(emp);
    }
}

// Client code
public class CompositePatternDemo {
    public static void main(String[] args) {
        Developer dev1 = new Developer("John", "Senior Developer");
        Developer dev2 = new Developer("David", "Junior Developer");
        
        Manager man1 = new Manager("Mike", "Department Manager");
        Manager man2 = new Manager("Sarah", "Project Manager");
        
        CompanyDirectory engDirectory = new CompanyDirectory();
        engDirectory.addEmployee(dev1);
        engDirectory.addEmployee(dev2);
        
        CompanyDirectory manDirectory = new CompanyDirectory();
        manDirectory.addEmployee(man1);
        manDirectory.addEmployee(man2);
        
        CompanyDirectory companyDirectory = new CompanyDirectory();
        companyDirectory.addEmployee(engDirectory);
        companyDirectory.addEmployee(manDirectory);
        
        companyDirectory.showEmployeeDetails();
    }
}
```

## Output

```
Developer: John, Position: Senior Developer
Developer: David, Position: Junior Developer
Manager: Mike, Position: Department Manager
Manager: Sarah, Position: Project Manager
```

## Advantages

1. **Simplifies client code** - Clients can treat composite structures and individual objects uniformly
2. **Makes it easier to add new kinds of components** - New component types can be added without changing existing code
3. **Provides flexibility** - The structure can be easily modified by adding or removing components

## Disadvantages

1. **Can be overly general** - The disadvantage of making it easy to add new components is that it makes it harder to restrict the components of a composite
2. **Type system issues** - Sometimes you want a composite to have only certain components, but the pattern doesn't enforce this

## Real-World Examples in Java

- Java AWT/Swing components (Component -> Container, JComponent, etc.)
- javax.faces.component.UIComponent in JSF
- org.w3c.dom.Node in XML processing

The Composite pattern is particularly useful when dealing with tree-like structures where you want to treat both individual objects and compositions uniformly.



# Proxy Design Pattern in Java

The Proxy design pattern is a structural pattern that provides a surrogate or placeholder for another object to control access to it. It's used when you want to add an extra layer of control over the access to an object.

## When to Use the Proxy Pattern

- When you need a more versatile or sophisticated reference to an object than a simple pointer
- For lazy initialization (virtual proxy)
- To control access to the original object (protection proxy)
- For logging requests (logging proxy)
- To cache expensive operations (caching proxy)
- For remote object communication (remote proxy)

## Types of Proxies

1. **Virtual Proxy** - Creates expensive objects on demand
2. **Protection Proxy** - Controls access to the original object
3. **Remote Proxy** - Provides a local representative for an object in a different address space
4. **Smart Reference Proxy** - Performs additional actions when an object is accessed

## Java Implementation Example

### 1. Basic Proxy Example

```java
// Subject interface
interface Image {
    void display();
}

// RealSubject
class RealImage implements Image {
    private String filename;
    
    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();
    }
    
    private void loadFromDisk() {
        System.out.println("Loading image: " + filename);
    }
    
    @Override
    public void display() {
        System.out.println("Displaying image: " + filename);
    }
}

// Proxy
class ProxyImage implements Image {
    private RealImage realImage;
    private String filename;
    
    public ProxyImage(String filename) {
        this.filename = filename;
    }
    
    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename); // Lazy initialization
        }
        realImage.display();
    }
}

// Client code
public class ProxyPatternDemo {
    public static void main(String[] args) {
        Image image1 = new ProxyImage("test1.jpg");
        Image image2 = new ProxyImage("test2.jpg");
        
        // Image will be loaded from disk only when displayed
        image1.display(); 
        image1.display(); // Image will not be loaded again
        image2.display();
    }
}
```

### 2. Protection Proxy Example

```java
// Subject interface
interface DatabaseAccess {
    void provideAccess();
}

// RealSubject
class RealDatabaseAccess implements DatabaseAccess {
    @Override
    public void provideAccess() {
        System.out.println("Access granted to database");
    }
}

// Protection Proxy
class ProtectionProxy implements DatabaseAccess {
    private RealDatabaseAccess realAccess;
    private String role;
    
    public ProtectionProxy(String role) {
        this.role = role;
    }
    
    @Override
    public void provideAccess() {
        if (role.equals("Admin")) {
            if (realAccess == null) {
                realAccess = new RealDatabaseAccess();
            }
            realAccess.provideAccess();
        } else {
            System.out.println("Access denied. Only Admins can access the database");
        }
    }
}

// Client code
public class ProtectionProxyDemo {
    public static void main(String[] args) {
        DatabaseAccess adminAccess = new ProtectionProxy("Admin");
        DatabaseAccess userAccess = new ProtectionProxy("User");
        
        adminAccess.provideAccess(); // Access granted
        userAccess.provideAccess(); // Access denied
    }
}
```

## Advantages

1. **Controlled access** - The proxy can control access to the real object
2. **Lazy initialization** - The proxy can create the real object only when needed
3. **Additional functionality** - The proxy can add functionality before or after forwarding the request
4. **Security** - Protection proxies can provide security checks
5. **Remote access** - Remote proxies can handle the communication with remote objects

## Disadvantages

1. **Increased response time** - Additional indirection may cause a slight delay
2. **Complexity** - Introduces additional classes into the design
3. **Potential overuse** - Might be used when simple delegation would suffice

## Real-World Examples in Java

1. **java.lang.reflect.Proxy** - Used to create dynamic proxies
2. **RMI (Remote Method Invocation)** - Uses stubs as remote proxies
3. **Spring AOP** - Uses proxies to implement aspect-oriented programming
4. **Hibernate** - Uses proxies for lazy loading of entities

## Dynamic Proxies in Java

Java provides built-in support for creating dynamic proxies at runtime:

```java
import java.lang.reflect.*;

// Subject interface
interface Calculator {
    int add(int a, int b);
    int subtract(int a, int b);
}

// RealSubject
class CalculatorImpl implements Calculator {
    @Override
    public int add(int a, int b) {
        return a + b;
    }
    
    @Override
    public int subtract(int a, int b) {
        return a - b;
    }
}

// InvocationHandler for dynamic proxy
class LoggingHandler implements InvocationHandler {
    private Object target;
    
    public LoggingHandler(Object target) {
        this.target = target;
    }
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before method: " + method.getName());
        Object result = method.invoke(target, args);
        System.out.println("After method: " + method.getName() + ", result: " + result);
        return result;
    }
}

// Client code
public class DynamicProxyDemo {
    public static void main(String[] args) {
        Calculator realCalculator = new CalculatorImpl();
        
        Calculator proxy = (Calculator) Proxy.newProxyInstance(
            Calculator.class.getClassLoader(),
            new Class[]{Calculator.class},
            new LoggingHandler(realCalculator)
        );
        
        System.out.println("Add result: " + proxy.add(5, 3));
        System.out.println("Subtract result: " + proxy.subtract(5, 3));
    }
}
```

This dynamic proxy example demonstrates how you can add logging functionality to method calls without modifying the original class.




# Flyweight Pattern with a Book Example

Let's explore the Flyweight pattern using a library management system where we need to track many copies of books efficiently.

## Problem Context

Imagine a library with thousands of book copies, but many of them are duplicates of the same title (like 50 copies of "Harry Potter"). Storing complete details for each copy would waste memory.

## Solution with Flyweight Pattern

We'll separate:
- **Intrinsic state** (shared book details: title, author, ISBN)
- **Extrinsic state** (unique to each copy: barcode, acquisition date, condition)

### Implementation

```java
import java.util.*;

// 1. Flyweight Interface (not always needed, but good practice)
interface Book {
    void display(String barcode);
}

// 2. Concrete Flyweight (shared book data)
class BookType implements Book {
    private final String title;
    private final String author;
    private final String isbn;
    
    public BookType(String title, String author, String isbn) {
        this.title = title;
        this.author = author;
        this.isbn = isbn;
    }
    
    @Override
    public void display(String barcode) {
        System.out.println("Title: " + title);
        System.out.println("Author: " + author);
        System.out.println("ISBN: " + isbn);
        System.out.println("Barcode: " + barcode);
        System.out.println("---");
    }
}

// 3. Flyweight Factory
class BookFactory {
    private static Map<String, BookType> bookTypes = new HashMap<>();
    
    public static BookType getBookType(String title, String author, String isbn) {
        String key = isbn; // ISBN makes a good unique key
        if (!bookTypes.containsKey(key)) {
            bookTypes.put(key, new BookType(title, author, isbn));
        }
        return bookTypes.get(key);
    }
}

// 4. Client (Library) with extrinsic state
class Library {
    private List<BookCopy> copies = new ArrayList<>();
    
    public void addBookCopy(String barcode, String title, 
                          String author, String isbn) {
        BookType type = BookFactory.getBookType(title, author, isbn);
        copies.add(new BookCopy(barcode, type));
    }
    
    public void displayAllBooks() {
        for (BookCopy copy : copies) {
            copy.display();
        }
    }
    
    // BookCopy wrapper for extrinsic state
    private class BookCopy {
        private String barcode;
        private BookType type;
        
        public BookCopy(String barcode, BookType type) {
            this.barcode = barcode;
            this.type = type;
        }
        
        public void display() {
            type.display(barcode);
        }
    }
}

// 5. Demo
public class LibraryDemo {
    public static void main(String[] args) {
        Library library = new Library();
        
        // Adding multiple copies of the same books
        library.addBookCopy("B001", "Design Patterns", "GoF", "978-0201633610");
        library.addBookCopy("B002", "Design Patterns", "GoF", "978-0201633610");
        library.addBookCopy("B003", "Clean Code", "Robert Martin", "978-0132350884");
        library.addBookCopy("B004", "Design Patterns", "GoF", "978-0201633610");
        library.addBookCopy("B005", "Clean Code", "Robert Martin", "978-0132350884");
        
        library.displayAllBooks();
        
        // Verify flyweight is working
        System.out.println("Total book types created: " + BookFactory.getBookTypeCount());
    }
    
    // Add this method to BookFactory for demonstration
    public static int getBookTypeCount() {
        return bookTypes.size();
    }
}
```

## Key Points in This Example

1. **Memory Efficiency**:
   - Only 2 `BookType` objects are created (for 5 book copies)
   - Shared data (title, author, ISBN) is stored once per book title

2. **Output**:
   ```
   Title: Design Patterns
   Author: GoF
   ISBN: 978-0201633610
   Barcode: B001
   ---
   Title: Design Patterns
   Author: GoF
   ISBN: 978-0201633610
   Barcode: B002
   ---
   Title: Clean Code
   Author: Robert Martin
   ISBN: 978-0132350884
   Barcode: B003
   ---
   Title: Design Patterns
   Author: GoF
   ISBN: 978-0201633610
   Barcode: B004
   ---
   Title: Clean Code
   Author: Robert Martin
   ISBN: 978-0132350884
   Barcode: B005
   ---
   Total book types created: 2
   ```

3. **Real-World Analogies**:
   - Like physical library catalog cards (shared book info) vs. individual copies (barcodes)
   - Similar to how ISBNs work in real publishing

## When This Pattern Shines

This implementation would be particularly valuable in:
1. Large library systems (public libraries, university systems)
2. Bookstore inventory management
3. Any system managing many instances of similar items
   - Music tracks (same song, different formats)
   - Product inventory (same model, different serial numbers)

The Flyweight pattern helps reduce memory usage by sharing common data while still maintaining individual identity for each copy through extrinsic state (like barcodes).



# Facade Design Pattern in Java

The Facade pattern is a structural design pattern that provides a simplified interface to a complex subsystem. It acts as a "front-facing" interface that hides the underlying complexity of a system.

## When to Use the Facade Pattern

- When you need to provide a simple interface to a complex subsystem
- When you want to decouple client code from subsystem components
- When there are many dependencies between clients and implementation classes
- When you want to layer your subsystems (facade provides entry point to each level)

## Key Components

1. **Facade** - The simplified interface for clients
2. **Subsystem Classes** - The complex system being simplified
3. **Client** - Uses the facade instead of calling subsystem objects directly

## Java Implementation Example

### Home Theater System Example

```java
// Subsystem components
class DVDPlayer {
    public void on() { System.out.println("DVD Player on"); }
    public void play(String movie) { System.out.println("Playing movie: " + movie); }
    public void off() { System.out.println("DVD Player off"); }
}

class Projector {
    public void on() { System.out.println("Projector on"); }
    public void wideScreenMode() { System.out.println("Projector in widescreen mode"); }
    public void off() { System.out.println("Projector off"); }
}

class SoundSystem {
    public void on() { System.out.println("Sound system on"); }
    public void setVolume(int level) { System.out.println("Sound volume set to " + level); }
    public void off() { System.out.println("Sound system off"); }
}

class TheaterLights {
    public void dim(int level) { System.out.println("Lights dimmed to " + level + "%"); }
    public void on() { System.out.println("Lights on"); }
}

// Facade
class HomeTheaterFacade {
    private DVDPlayer dvdPlayer;
    private Projector projector;
    private SoundSystem soundSystem;
    private TheaterLights lights;
    
    public HomeTheaterFacade(DVDPlayer dvdPlayer, Projector projector, 
                           SoundSystem soundSystem, TheaterLights lights) {
        this.dvdPlayer = dvdPlayer;
        this.projector = projector;
        this.soundSystem = soundSystem;
        this.lights = lights;
    }
    
    public void watchMovie(String movie) {
        System.out.println("Get ready to watch a movie...");
        lights.dim(10);
        projector.on();
        projector.wideScreenMode();
        soundSystem.on();
        soundSystem.setVolume(50);
        dvdPlayer.on();
        dvdPlayer.play(movie);
    }
    
    public void endMovie() {
        System.out.println("Shutting down the theater...");
        dvdPlayer.off();
        soundSystem.off();
        projector.off();
        lights.on();
    }
}

// Client
public class FacadePatternDemo {
    public static void main(String[] args) {
        // Create subsystem components
        DVDPlayer dvdPlayer = new DVDPlayer();
        Projector projector = new Projector();
        SoundSystem soundSystem = new SoundSystem();
        TheaterLights lights = new TheaterLights();
        
        // Create facade
        HomeTheaterFacade homeTheater = new HomeTheaterFacade(
            dvdPlayer, projector, soundSystem, lights);
        
        // Simplified interface
        homeTheater.watchMovie("Inception");
        System.out.println("\nEnjoy the movie!\n");
        homeTheater.endMovie();
    }
}
```

### Output:
```
Get ready to watch a movie...
Lights dimmed to 10%
Projector on
Projector in widescreen mode
Sound system on
Sound volume set to 50
DVD Player on
Playing movie: Inception

Enjoy the movie!

Shutting down the theater...
DVD Player off
Sound system off
Projector off
Lights on
```

## Advantages

1. **Simplifies complex systems** - Provides a simple interface to complex functionality
2. **Decouples clients** - Clients don't need to know about subsystem internals
3. **Improves maintainability** - Changes to subsystem don't affect clients
4. **Promotes weak coupling** - Reduces dependencies between subsystems and clients

## Disadvantages

1. **Can become a "god object"** - If overused, facade can become too large
2. **Limited flexibility** - Clients needing advanced features may need to bypass facade
3. **Additional layer** - Introduces another layer of abstraction

## Real-World Examples in Java

1. **JDBC** - The DriverManager class acts as a facade for database connections
2. **Servlet API** - HttpServletRequest and HttpServletResponse simplify HTTP handling
3. **SLF4J** - Simple Logging Facade for Java hides different logging implementations
4. **Spring Framework** - Many components provide simplified interfaces to complex services

## Variations

1. **Transparent Facade** - Still allows access to subsystem components when needed
2. **Opaque Facade** - Completely hides the subsystem from clients
3. **Multiple Facades** - Different facades for different client needs

## When to Choose Facade Over Other Patterns

- **Facade vs Adapter**: Facade simplifies, Adapter converts interfaces
- **Facade vs Proxy**: Facade provides new interface, Proxy uses same interface
- **Facade vs Mediator**: Facade is one-way (client to subsystem), Mediator coordinates between colleagues

The Facade pattern is particularly useful when:
- You need to provide a simple interface to a legacy system
- You want to structure a subsystem into layers
- You need to reduce dependencies between clients and complex subsystems