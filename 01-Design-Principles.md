# Design Principles & Patterns

## Table of Contents
1. [SOLID Principles](#solid-principles)
2. [Other Design Principles](#other-design-principles)
3. [Creational Design Patterns](#creational-design-patterns)
4. [Structural Design Patterns](#structural-design-patterns)
5. [Behavioral Design Patterns](#behavioral-design-patterns)
6. [Best Practices](#best-practices)

---

## SOLID Principles

### 1. Single Responsibility Principle (SRP)
**Definition**: A class should have only one reason to change.

**Key Points**:
- Each class should have a single, well-defined purpose
- Separation of concerns
- Easier to maintain and test

**Example**:
```java
// ❌ Bad: Multiple responsibilities
class User {
    void saveToDatabase() { }
    void sendEmail() { }
    void generateReport() { }
}

// ✅ Good: Single responsibility
class User {
    // Only user data management
}

class UserRepository {
    void save(User user) { }
}

class EmailService {
    void sendEmail(User user) { }
}
```

**Interview Questions**:
- How do you identify if a class violates SRP?
- What are the benefits of following SRP?

---

### 2. Open/Closed Principle (OCP)
**Definition**: Software entities should be open for extension but closed for modification.

**Key Points**:
- Use abstraction and polymorphism
- Extend functionality through inheritance or composition
- Avoid modifying existing, tested code

**Example**:
```java
// ✅ Good: Open for extension, closed for modification
interface PaymentProcessor {
    void processPayment(double amount);
}

class CreditCardProcessor implements PaymentProcessor {
    public void processPayment(double amount) {
        // Credit card logic
    }
}

class PayPalProcessor implements PaymentProcessor {
    public void processPayment(double amount) {
        // PayPal logic
    }
}
```

**Interview Questions**:
- How does OCP relate to polymorphism?
- Give an example where OCP prevents bugs.

---

### 3. Liskov Substitution Principle (LSP)
**Definition**: Objects of a superclass should be replaceable with objects of its subclasses without breaking the application.

**Key Points**:
- Subtypes must be substitutable for their base types
- Preconditions cannot be strengthened in subtypes
- Postconditions cannot be weakened in subtypes

**Example**:
```java
// ❌ Bad: Violates LSP
class Rectangle {
    protected int width, height;
    void setWidth(int w) { width = w; }
    void setHeight(int h) { height = h; }
}

class Square extends Rectangle {
    void setWidth(int w) {
        width = height = w; // Violates LSP
    }
}

// ✅ Good: Proper inheritance
interface Shape {
    int getArea();
}

class Rectangle implements Shape {
    private int width, height;
    public int getArea() { return width * height; }
}

class Square implements Shape {
    private int side;
    public int getArea() { return side * side; }
}
```

**Interview Questions**:
- What happens when LSP is violated?
- How do you test for LSP compliance?

---

### 4. Interface Segregation Principle (ISP)
**Definition**: Clients should not be forced to depend on interfaces they don't use.

**Key Points**:
- Create specific interfaces rather than general-purpose ones
- Avoid "fat" interfaces
- Better cohesion and less coupling

**Example**:
```java
// ❌ Bad: Fat interface
interface Worker {
    void work();
    void eat();
    void sleep();
}

// ✅ Good: Segregated interfaces
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}

class Human implements Workable, Eatable, Sleepable {
    // Implementation
}

class Robot implements Workable {
    // Only implements work
}
```

**Interview Questions**:
- How does ISP improve code maintainability?
- What's the difference between ISP and SRP?

---

### 5. Dependency Inversion Principle (DIP)
**Definition**: High-level modules should not depend on low-level modules. Both should depend on abstractions.

**Key Points**:
- Depend on abstractions, not concretions
- Use dependency injection
- Inversion of Control (IoC)

**Example**:
```java
// ❌ Bad: High-level depends on low-level
class EmailService {
    private MySQLDatabase database;
    public EmailService() {
        this.database = new MySQLDatabase();
    }
}

// ✅ Good: Depends on abstraction
interface Database {
    void save(String data);
}

class EmailService {
    private Database database;
    public EmailService(Database database) {
        this.database = database; // Dependency Injection
    }
}
```

**Interview Questions**:
- How does DIP enable testability?
- Explain dependency injection patterns.

---

## Other Design Principles

### DRY (Don't Repeat Yourself)
- Avoid code duplication
- Extract common functionality
- Use abstraction and inheritance

### KISS (Keep It Simple, Stupid)
- Simplicity should be a key goal
- Avoid unnecessary complexity
- Prefer simple solutions

### YAGNI (You Aren't Gonna Need It)
- Don't add functionality until needed
- Avoid over-engineering
- Focus on current requirements

### Separation of Concerns
- Divide application into distinct sections
- Each section addresses a separate concern
- Improves maintainability

---

## Creational Design Patterns

### 1. Singleton Pattern
**Purpose**: Ensure a class has only one instance and provide global access.

**Use Cases**:
- Database connections
- Logger instances
- Configuration objects

**Implementation**:
```java
// Thread-safe Singleton
class Singleton {
    private static volatile Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**Interview Questions**:
- How do you make Singleton thread-safe?
- What are the drawbacks of Singleton pattern?

---

### 2. Factory Pattern
**Purpose**: Create objects without specifying the exact class.

**Types**:
- Simple Factory
- Factory Method
- Abstract Factory

**Example**:
```java
interface Animal {
    void makeSound();
}

class Dog implements Animal {
    public void makeSound() { System.out.println("Woof"); }
}

class Cat implements Animal {
    public void makeSound() { System.out.println("Meow"); }
}

class AnimalFactory {
    public static Animal createAnimal(String type) {
        if ("dog".equals(type)) return new Dog();
        if ("cat".equals(type)) return new Cat();
        throw new IllegalArgumentException("Unknown animal type");
    }
}
```

**Interview Questions**:
- Difference between Factory Method and Abstract Factory?
- When to use Factory vs. Builder pattern?

---

### 3. Builder Pattern
**Purpose**: Construct complex objects step by step.

**Use Cases**:
- Objects with many optional parameters
- Immutable object creation
- Fluent API design

**Example**:
```java
class Pizza {
    private String dough;
    private String sauce;
    private String topping;
    
    private Pizza(Builder builder) {
        this.dough = builder.dough;
        this.sauce = builder.sauce;
        this.topping = builder.topping;
    }
    
    static class Builder {
        private String dough;
        private String sauce;
        private String topping;
        
        Builder dough(String dough) {
            this.dough = dough;
            return this;
        }
        
        Builder sauce(String sauce) {
            this.sauce = sauce;
            return this;
        }
        
        Builder topping(String topping) {
            this.topping = topping;
            return this;
        }
        
        Pizza build() {
            return new Pizza(this);
        }
    }
}

// Usage
Pizza pizza = new Pizza.Builder()
    .dough("thin")
    .sauce("tomato")
    .topping("cheese")
    .build();
```

**Interview Questions**:
- When is Builder pattern preferred over constructor?
- How does Builder help with immutability?

---

### 4. Prototype Pattern
**Purpose**: Create objects by cloning existing instances.

**Use Cases**:
- Expensive object creation
- Similar objects with slight variations
- Runtime object creation

---

## Structural Design Patterns

### 1. Adapter Pattern
**Purpose**: Allow incompatible interfaces to work together.

**Example**:
```java
// Existing interface
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Advanced interface
interface AdvancedMediaPlayer {
    void playVlc(String fileName);
    void playMp4(String fileName);
}

// Adapter
class MediaAdapter implements MediaPlayer {
    AdvancedMediaPlayer advancedPlayer;
    
    public MediaAdapter(String audioType) {
        if (audioType.equals("vlc")) {
            advancedPlayer = new VlcPlayer();
        } else if (audioType.equals("mp4")) {
            advancedPlayer = new Mp4Player();
        }
    }
    
    public void play(String audioType, String fileName) {
        if (audioType.equals("vlc")) {
            advancedPlayer.playVlc(fileName);
        } else if (audioType.equals("mp4")) {
            advancedPlayer.playMp4(fileName);
        }
    }
}
```

---

### 2. Decorator Pattern
**Purpose**: Add behavior to objects dynamically.

**Use Cases**:
- Extending functionality without subclassing
- Runtime behavior modification
- Wrapper pattern

**Example**:
```java
interface Coffee {
    double getCost();
    String getDescription();
}

class SimpleCoffee implements Coffee {
    public double getCost() { return 2.0; }
    public String getDescription() { return "Simple coffee"; }
}

abstract class CoffeeDecorator implements Coffee {
    protected Coffee coffee;
    
    public CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }
}

class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }
    
    public double getCost() {
        return coffee.getCost() + 0.5;
    }
    
    public String getDescription() {
        return coffee.getDescription() + ", Milk";
    }
}
```

---

### 3. Facade Pattern
**Purpose**: Provide a simplified interface to a complex subsystem.

**Use Cases**:
- Hiding system complexity
- Providing a unified interface
- Reducing dependencies

---

### 4. Proxy Pattern
**Purpose**: Provide a placeholder for another object to control access.

**Types**:
- Virtual Proxy: Lazy loading
- Protection Proxy: Access control
- Remote Proxy: Network communication

---

## Behavioral Design Patterns

### 1. Observer Pattern
**Purpose**: Define a one-to-many dependency between objects.

**Use Cases**:
- Event handling systems
- Model-View architecture
- Publish-Subscribe systems

**Example**:
```java
interface Observer {
    void update(String message);
}

interface Subject {
    void attach(Observer observer);
    void detach(Observer observer);
    void notifyObservers();
}

class NewsAgency implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String news;
    
    public void attach(Observer observer) {
        observers.add(observer);
    }
    
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(news);
        }
    }
    
    public void setNews(String news) {
        this.news = news;
        notifyObservers();
    }
}
```

---

### 2. Strategy Pattern
**Purpose**: Define a family of algorithms, encapsulate each, and make them interchangeable.

**Use Cases**:
- Multiple ways to perform a task
- Runtime algorithm selection
- Eliminating conditional statements

**Example**:
```java
interface PaymentStrategy {
    void pay(int amount);
}

class CreditCardStrategy implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Credit Card");
    }
}

class PayPalStrategy implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using PayPal");
    }
}

class ShoppingCart {
    private PaymentStrategy strategy;
    
    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void checkout(int amount) {
        strategy.pay(amount);
    }
}
```

---

### 3. Command Pattern
**Purpose**: Encapsulate a request as an object.

**Use Cases**:
- Undo/Redo functionality
- Queue operations
- Logging requests

---

### 4. Chain of Responsibility
**Purpose**: Pass requests along a chain of handlers.

**Use Cases**:
- Request processing pipelines
- Event handling
- Middleware patterns

---

## Best Practices

### Code Quality
1. **Naming Conventions**: Use descriptive names
2. **Comments**: Explain why, not what
3. **Function Size**: Keep functions small and focused
4. **Error Handling**: Handle exceptions appropriately

### Design Guidelines
1. **Favor Composition over Inheritance**
2. **Program to Interfaces, not Implementations**
3. **Encapsulate What Varies**
4. **Depend on Abstractions**

### Testing
1. **Unit Testing**: Test individual components
2. **Integration Testing**: Test component interactions
3. **Mocking**: Use mocks for dependencies
4. **Test Coverage**: Aim for high coverage

---

## Interview Preparation Tips

### Common Questions
1. Explain SOLID principles with examples
2. When would you use Factory vs. Builder?
3. Difference between Strategy and State pattern?
4. How does Observer pattern work?
5. Explain Dependency Injection

### Practice Exercises
1. Refactor code to follow SOLID principles
2. Implement design patterns from scratch
3. Identify pattern usage in existing code
4. Design a system using multiple patterns

---

## Advanced Interview Questions & Answers

### Q1: How do you refactor a class that violates Single Responsibility Principle?

**Answer**:
When a class violates SRP, it has multiple reasons to change. To refactor:

1. **Identify Responsibilities**: Analyze the class and list all distinct responsibilities
2. **Extract Classes**: Create separate classes for each responsibility
3. **Use Composition**: Compose the original class using the new classes
4. **Maintain Interface**: Keep the original interface if needed, delegate to new classes

**Example**:
```java
// Before: Violates SRP
class User {
    void saveToDatabase() { }
    void sendEmail() { }
    void generateReport() { }
}

// After: Follows SRP
class User {
    // Only user data
}

class UserRepository {
    void save(User user) { }
}

class EmailService {
    void sendEmail(User user) { }
}

class ReportGenerator {
    void generateReport(User user) { }
}
```

**Benefits**:
- Easier to test each responsibility independently
- Changes to one responsibility don't affect others
- Better code organization and maintainability

---

### Q2: Explain the Open/Closed Principle with a real-world example.

**Answer**:
OCP states that software entities should be open for extension but closed for modification.

**Real-world Example - Payment Processing**:

**Problem**: Adding new payment methods requires modifying existing code.

**Solution**: Use abstraction and polymorphism.

```java
// Closed for modification
interface PaymentProcessor {
    void processPayment(double amount);
}

// Open for extension
class CreditCardProcessor implements PaymentProcessor {
    public void processPayment(double amount) {
        // Credit card logic
    }
}

class PayPalProcessor implements PaymentProcessor {
    public void processPayment(double amount) {
        // PayPal logic
    }
}

// New payment method - no modification to existing code
class CryptocurrencyProcessor implements PaymentProcessor {
    public void processPayment(double amount) {
        // Crypto logic
    }
}
```

**Key Points**:
- Existing code doesn't change when adding new payment methods
- New functionality added through new classes
- Uses polymorphism to handle different implementations
- Follows dependency inversion principle

---

### Q3: How does the Strategy pattern differ from the State pattern? When would you use each?

**Answer**:

**Strategy Pattern**:
- **Purpose**: Encapsulate algorithms and make them interchangeable
- **Focus**: Different ways to perform a task
- **Context**: Chooses strategy, doesn't change behavior
- **Example**: Payment methods, sorting algorithms, compression algorithms

**State Pattern**:
- **Purpose**: Allow object to alter behavior when internal state changes
- **Focus**: Behavior changes based on state
- **Context**: State changes, behavior changes automatically
- **Example**: Order states (pending, processing, shipped), vending machine states

**Key Differences**:

| Aspect | Strategy | State |
|--------|----------|-------|
| Intent | Algorithm selection | State-based behavior |
| When to use | Multiple ways to do something | Object behavior depends on state |
| Who chooses | Client/Context | State transitions |
| Change frequency | Runtime selection | State changes trigger behavior |

**When to Use Strategy**:
- Multiple algorithms for same task
- Want to avoid conditional statements
- Algorithms can be selected at runtime
- Example: Different sorting algorithms, payment processors

**When to Use State**:
- Object behavior depends on state
- Many conditional statements based on state
- State transitions are well-defined
- Example: Order processing, game character states, workflow engines

---

### Q4: Explain the difference between Factory Method and Abstract Factory patterns.

**Answer**:

**Factory Method Pattern**:
- **Purpose**: Create objects without specifying exact class
- **Structure**: One factory class with factory method
- **Products**: Creates one type of product
- **Complexity**: Simpler

**Abstract Factory Pattern**:
- **Purpose**: Create families of related objects
- **Structure**: Multiple factory methods in one factory
- **Products**: Creates multiple related products
- **Complexity**: More complex

**Example - Factory Method**:
```java
abstract class VehicleFactory {
    abstract Vehicle createVehicle();
}

class CarFactory extends VehicleFactory {
    Vehicle createVehicle() {
        return new Car();
    }
}
```

**Example - Abstract Factory**:
```java
interface GUIFactory {
    Button createButton();
    Dialog createDialog();
}

class WindowsFactory implements GUIFactory {
    Button createButton() { return new WindowsButton(); }
    Dialog createDialog() { return new WindowsDialog(); }
}

class MacFactory implements GUIFactory {
    Button createButton() { return new MacButton(); }
    Dialog createDialog() { return new MacDialog(); }
}
```

**When to Use Factory Method**:
- Creating single type of object
- Subclasses decide which class to instantiate
- Simple object creation

**When to Use Abstract Factory**:
- Creating families of related objects
- Need to ensure products are compatible
- System should be independent of how products are created

---

### Q5: How do you implement a thread-safe Singleton pattern? What are the trade-offs?

**Answer**:

**1. Eager Initialization**:
```java
class Singleton {
    private static final Singleton instance = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() {
        return instance;
    }
}
```
- **Pros**: Simple, thread-safe
- **Cons**: Created even if not used

**2. Lazy Initialization with Synchronization**:
```java
class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
- **Pros**: Created only when needed
- **Cons**: Synchronization overhead on every call

**3. Double-Checked Locking**:
```java
class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
- **Pros**: Thread-safe, lazy initialization, minimal synchronization
- **Cons**: More complex, requires volatile keyword

**4. Bill Pugh Solution (Inner Static Class)**:
```java
class Singleton {
    private Singleton() {}
    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```
- **Pros**: Thread-safe, lazy initialization, simple, no synchronization needed
- **Cons**: Slightly more complex structure

**5. Enum Singleton (Recommended)**:
```java
enum Singleton {
    INSTANCE;
    public void doSomething() {
        // Implementation
    }
}
```
- **Pros**: Thread-safe, serialization-safe, reflection-safe, simplest
- **Cons**: Less flexible (can't extend classes)

**Trade-offs Summary**:
- **Performance**: Eager vs Lazy initialization
- **Complexity**: Simple vs optimized implementations
- **Flexibility**: Enum vs class-based
- **Serialization**: Some implementations need special handling

---

### Q6: Explain Dependency Injection and its benefits. What are the different types?

**Answer**:

**Dependency Injection (DI)** is a design pattern where objects receive their dependencies from external sources rather than creating them internally.

**Types of Dependency Injection**:

**1. Constructor Injection**:
```java
class UserService {
    private UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```
- **Pros**: Dependencies are explicit, immutable, testable
- **Cons**: Many dependencies can make constructor unwieldy

**2. Setter Injection**:
```java
class UserService {
    private UserRepository repository;
    
    public void setRepository(UserRepository repository) {
        this.repository = repository;
    }
}
```
- **Pros**: Flexible, optional dependencies
- **Cons**: Dependencies may not be set, mutable

**3. Interface Injection**:
```java
interface Injectable {
    void inject(UserRepository repository);
}

class UserService implements Injectable {
    private UserRepository repository;
    
    public void inject(UserRepository repository) {
        this.repository = repository;
    }
}
```
- **Pros**: Explicit injection contract
- **Cons**: More complex, less common

**Benefits**:
1. **Loose Coupling**: Dependencies are abstracted
2. **Testability**: Easy to mock dependencies
3. **Flexibility**: Can swap implementations
4. **Maintainability**: Changes isolated to specific classes
5. **Reusability**: Components can be reused in different contexts

**Dependency Injection Container**:
- Manages object creation and dependencies
- Examples: Spring Framework, Google Guice, Dagger
- Handles lifecycle, scoping, and wiring

---

### Q7: What is the difference between Adapter and Decorator patterns?

**Answer**:

**Adapter Pattern**:
- **Purpose**: Make incompatible interfaces work together
- **Intent**: Convert interface of one class to another
- **Structure**: Wraps incompatible object
- **When to use**: Need to use existing class with incompatible interface

**Decorator Pattern**:
- **Purpose**: Add behavior to objects dynamically
- **Intent**: Attach additional responsibilities to object
- **Structure**: Wraps object to add functionality
- **When to use**: Need to add behavior without modifying class

**Key Differences**:

| Aspect | Adapter | Decorator |
|--------|---------|-----------|
| Purpose | Interface conversion | Behavior addition |
| Relationship | Has-a (composition) | Has-a (composition) |
| Interface | Changes interface | Maintains interface |
| Intent | Make incompatible work | Add functionality |
| Example | Legacy system integration | Adding features to objects |

**Adapter Example**:
```java
// Existing interface
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Incompatible interface
interface AdvancedMediaPlayer {
    void playVlc(String fileName);
    void playMp4(String fileName);
}

// Adapter makes them compatible
class MediaAdapter implements MediaPlayer {
    AdvancedMediaPlayer advancedPlayer;
    
    public void play(String audioType, String fileName) {
        if (audioType.equals("vlc")) {
            advancedPlayer.playVlc(fileName);
        }
    }
}
```

**Decorator Example**:
```java
interface Coffee {
    double getCost();
    String getDescription();
}

class SimpleCoffee implements Coffee {
    public double getCost() { return 2.0; }
    public String getDescription() { return "Simple coffee"; }
}

class MilkDecorator implements Coffee {
    private Coffee coffee;
    
    public MilkDecorator(Coffee coffee) {
        this.coffee = coffee;
    }
    
    public double getCost() {
        return coffee.getCost() + 0.5;
    }
    
    public String getDescription() {
        return coffee.getDescription() + ", Milk";
    }
}
```

---

### Q8: How would you design a system that needs to support multiple notification channels (email, SMS, push) using design patterns?

**Answer**:

**Approach**: Use Strategy pattern for notification channels and Factory pattern for creation.

**Design**:

```java
// Strategy interface
interface NotificationStrategy {
    void send(String message, String recipient);
}

// Concrete strategies
class EmailNotification implements NotificationStrategy {
    public void send(String message, String recipient) {
        // Email sending logic
    }
}

class SMSNotification implements NotificationStrategy {
    public void send(String message, String recipient) {
        // SMS sending logic
    }
}

class PushNotification implements NotificationStrategy {
    public void send(String message, String recipient) {
        // Push notification logic
    }
}

// Factory for creating strategies
class NotificationFactory {
    public static NotificationStrategy create(String type) {
        switch(type) {
            case "email": return new EmailNotification();
            case "sms": return new SMSNotification();
            case "push": return new PushNotification();
            default: throw new IllegalArgumentException("Unknown type");
        }
    }
}

// Context that uses strategy
class NotificationService {
    private NotificationStrategy strategy;
    
    public void setStrategy(NotificationStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void notify(String message, String recipient) {
        strategy.send(message, recipient);
    }
}
```

**Benefits**:
- **Open/Closed Principle**: Add new channels without modifying existing code
- **Single Responsibility**: Each strategy handles one channel
- **Flexibility**: Can change strategy at runtime
- **Testability**: Easy to mock strategies

**Enhancements**:
- Use Builder pattern for complex notification configuration
- Use Observer pattern for notification events
- Use Chain of Responsibility for fallback mechanisms

---

### Q9: Explain the Command pattern and when you would use it.

**Answer**:

**Command Pattern** encapsulates a request as an object, allowing you to parameterize clients with different requests, queue requests, and support undo operations.

**Structure**:
```java
// Command interface
interface Command {
    void execute();
    void undo();
}

// Concrete command
class LightOnCommand implements Command {
    private Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }
    
    public void execute() {
        light.turnOn();
    }
    
    public void undo() {
        light.turnOff();
    }
}

// Invoker
class RemoteControl {
    private Command command;
    
    public void setCommand(Command command) {
        this.command = command;
    }
    
    public void pressButton() {
        command.execute();
    }
}
```

**Use Cases**:

1. **Undo/Redo Functionality**:
   - Text editors, graphics applications
   - Maintain command history
   - Execute and undo operations

2. **Macro Recording**:
   - Record sequence of commands
   - Replay commands
   - Batch operations

3. **Queue Operations**:
   - Queue commands for later execution
   - Background processing
   - Asynchronous execution

4. **Logging and Auditing**:
   - Log all commands
   - Audit trail
   - Replay for debugging

5. **Transaction Management**:
   - Database transactions
   - Rollback support
   - Atomic operations

**Benefits**:
- Decouples invoker from receiver
- Easy to add new commands
- Supports undo/redo
- Can queue and log commands
- Supports macro operations

---

### Q10: How do you handle the violation of Liskov Substitution Principle in a real-world scenario?

**Answer**:

**LSP Violation Example - Rectangle and Square**:

```java
// Violates LSP
class Rectangle {
    protected int width, height;
    
    void setWidth(int w) { width = w; }
    void setHeight(int h) { height = h; }
    int getArea() { return width * height; }
}

class Square extends Rectangle {
    void setWidth(int w) {
        width = w;
        height = w; // Violates LSP - unexpected behavior
    }
    
    void setHeight(int h) {
        width = h;
        height = h; // Violates LSP
    }
}

// Client code breaks
void testRectangle(Rectangle r) {
    r.setWidth(5);
    r.setHeight(4);
    assert r.getArea() == 20; // Fails for Square!
}
```

**Solution - Use Composition or Separate Interfaces**:

```java
// Solution 1: Separate interfaces
interface Shape {
    int getArea();
}

class Rectangle implements Shape {
    private int width, height;
    
    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }
    
    public int getArea() {
        return width * height;
    }
}

class Square implements Shape {
    private int side;
    
    public Square(int side) {
        this.side = side;
    }
    
    public int getArea() {
        return side * side;
    }
}

// Solution 2: Immutable objects
class Rectangle {
    private final int width, height;
    
    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }
    
    public int getArea() {
        return width * height;
    }
}
```

**Key Principles**:
1. **Preconditions cannot be strengthened**: Subtype can't require more than base type
2. **Postconditions cannot be weakened**: Subtype must provide at least what base type promises
3. **Invariants must be preserved**: Subtype must maintain base type invariants
4. **History constraint**: Subtype can't introduce methods that base type doesn't allow

**Detection**:
- If you need to check type before using, LSP is likely violated
- If subtype changes behavior in unexpected ways, LSP is violated
- If client code breaks when substituting subtype, LSP is violated

---

## Resources
- Design Patterns: Elements of Reusable Object-Oriented Software (Gang of Four)
- Clean Code by Robert C. Martin
- Refactoring by Martin Fowler

