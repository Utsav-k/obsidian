# SOLID Principles Summary:

SOLID Summary:
S - Single Responsibility: Employee only holds data, PayrollCalculator only calculates pay
O - Open/Closed: Add new Triangle shape without changing existing Shape code
L - Liskov Substitution: Duck and Eagle can replace Bird without breaking BirdHandler
I - Interface Segregation: SimplePrinter only implements Printer, not unused methods
D - Dependency Inversion: UserService depends on Database interface, not concrete MySQLDatabase

## Benefits:

- ✅ **Maintainable** - Easy to modify
- ✅ **Testable** - Easy to mock dependencies
- ✅ **Flexible** - Easy to extend
- ✅ **Reusable** - Loose coupling between components

**Quick Rule:** If changing one thing breaks multiple classes, you're probably violating SOLID principles!


```java
// 1. SINGLE RESPONSIBILITY PRINCIPLE (SRP)
// One class = One reason to change

// ❌ BAD - Multiple responsibilities
class BadEmployee {
    private String name;
    
    public void calculatePay() { /* pay logic */ }
    public void saveToDatabase() { /* database logic */ }
    public void printReport() { /* printing logic */ }
}

// ✅ GOOD - Single responsibility each
class Employee {
    private String name;
    public String getName() { return name; }
}

class PayrollCalculator {
    public void calculatePay(Employee emp) { /* pay logic */ }
}

class EmployeeRepository {
    public void save(Employee emp) { /* database logic */ }
}

// 2. OPEN/CLOSED PRINCIPLE (OCP)
// Open for extension, closed for modification

abstract class Shape {
    public abstract double area();
}

class Circle extends Shape {
    private double radius;
    public Circle(double radius) { this.radius = radius; }
    
    @Override
    public double area() { return Math.PI * radius * radius; }
}

class Square extends Shape {
    private double side;
    public Square(double side) { this.side = side; }
    
    @Override
    public double area() { return side * side; }
}

// Adding Triangle doesn't modify existing code ✅
class Triangle extends Shape {
    private double base, height;
    public Triangle(double base, double height) { 
        this.base = base; 
        this.height = height; 
    }
    
    @Override
    public double area() { return 0.5 * base * height; }
}

// 3. LISKOV SUBSTITUTION PRINCIPLE (LSP)
// Subtypes must be substitutable for base type

class Bird {
    public void fly() { System.out.println("Flying"); }
}

class Duck extends Bird {
    @Override
    public void fly() { System.out.println("Duck flying"); }
}

class Eagle extends Bird {
    @Override
    public void fly() { System.out.println("Eagle soaring"); }
}

// Both Duck and Eagle can substitute Bird without issues
class BirdHandler {
    public void makeBirdFly(Bird bird) {
        bird.fly(); // Works for any Bird subtype
    }
}

// 4. INTERFACE SEGREGATION PRINCIPLE (ISP)
// Don't force classes to implement unused methods

// ❌ BAD - Fat interface
interface BadPrinter {
    void print();
    void scan();
    void fax();
}

// ✅ GOOD - Segregated interfaces
interface Printer {
    void print();
}

interface Scanner {
    void scan();
}

class SimplePrinter implements Printer {
    @Override
    public void print() { System.out.println("Printing..."); }
}

class MultiFunctionPrinter implements Printer, Scanner {
    @Override
    public void print() { System.out.println("Printing..."); }
    
    @Override
    public void scan() { System.out.println("Scanning..."); }
}

// 5. DEPENDENCY INVERSION PRINCIPLE (DIP)
// Depend on abstractions, not concrete classes

interface Database {
    void save(String data);
}

class MySQLDatabase implements Database {
    @Override
    public void save(String data) {
        System.out.println("Saving to MySQL: " + data);
    }
}

class PostgreSQLDatabase implements Database {
    @Override
    public void save(String data) {
        System.out.println("Saving to PostgreSQL: " + data);
    }
}

// UserService depends on abstraction, not concrete database
class UserService {
    private Database database;
    
    public UserService(Database database) {
        this.database = database;  // Dependency injection
    }
    
    public void createUser(String userData) {
        database.save(userData);  // Works with any Database implementation
    }
}

// DEMO
public class SOLIDQuickDemo {
    public static void main(String[] args) {
        // LSP Example
        BirdHandler handler = new BirdHandler();
        handler.makeBirdFly(new Duck());
        handler.makeBirdFly(new Eagle());
        
        // DIP Example
        UserService mysqlService = new UserService(new MySQLDatabase());
        UserService postgresService = new UserService(new PostgreSQLDatabase());
        
        mysqlService.createUser("John Doe");
        postgresService.createUser("Jane Smith");
    }
}
```