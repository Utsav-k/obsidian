## Interfaces & Abstract Classes

```java
// INTERFACE - Just contracts, multiple inheritance possible
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

class Duck implements Flyable, Swimmable {  // Multiple interfaces ✓
    public void fly() { System.out.println("Duck flies"); }
    public void swim() { System.out.println("Duck swims"); }
}

// ABSTRACT CLASS - Shared code + abstract methods
abstract class Animal {
    protected String name;
    
    public Animal(String name) { this.name = name; }  // Constructor ✓
    
    public void eat() { System.out.println(name + " eats"); }  // Shared code ✓
    
    public abstract void makeSound();  // Must implement
}

class Dog extends Animal {
    public Dog(String name) { super(name); }
    public void makeSound() { System.out.println(name + " barks"); }
}

// DEMO
public class QuickDemo {
    public static void main(String[] args) {
        Duck duck = new Duck();
        duck.fly();    // From Flyable
        duck.swim();   // From Swimmable
        
        Dog dog = new Dog("Buddy");
        dog.eat();         // Inherited shared method
        dog.makeSound();   // Implemented abstract method
    }
}
```

#### Modern Best Practices:

Choose Interface when:

- Need multiple inheritance (Duck implements Flyable, Swimmable)
- Just defining contracts/behavior
- Unrelated classes need same methods

Choose Abstract Class when:

- Have common code to share (eat() method)
- Need constructors or instance variables
- Classes are closely related

Use interfaces by default for contracts and behaviors. Use abstract classes when you have significant shared implementation between closely related classes. Often, the best approach is using both together.


### Interfaces in Java - Complete Guide

Interface = A contract that defines what methods a class must implement. Pure abstraction with no state (except constants).
Key Features:

- All methods are public abstract by default
- All variables are public static final (constants)
- Classes can implement multiple interfaces
- Interfaces can extend other interfaces
- Java 8+: Can have default and static methods

### Key Concepts Explained:

Basic Interface

    Define contracts with abstract methods
    Constants are public static final by default

Multiple Inheritance

    Classes can implement multiple interfaces
    Solves the "diamond problem" of multiple inheritance

Generic Interfaces

```java
    javaContainer<T>  // T is a type parameter
    MyList<String> stringList;  // Concrete type
```

Bounded Generics
```java
    javaRepository<T extends Comparable<T>>  // T must be Comparable
```
Functional Interfaces

    Have exactly one abstract method
    Can be used with lambda expressions
    @FunctionalInterface annotation ensures single method

Default Methods (Java 8+)

    Provide implementation in interface
    Allow adding new methods without breaking existing code
    Can be overridden by implementing classes

When to Use Interfaces:

    ✅ Define contracts/APIs
    ✅ Multiple inheritance needed
    ✅ Loose coupling between classes
    ✅ Functional programming (lambdas)
    ✅ Strategy pattern, Dependency injection

Best Practices:

    Keep interfaces focused (single responsibility)
    Use generics for type safety
    Prefer composition over inheritance
    Use functional interfaces for simple operations


```java
// Generics
interface Container<T> {
    void add(T item);
    T get(int index);
    int size();
    boolean isEmpty();
}

class MyList<T> implements Container<T> {
    private Object[] items = new Object[10];
    private int count = 0;
    
    @Override
    public void add(T item) {
        if (count < items.length) {
            items[count++] = item;
        }
    }
    
    @Override
    @SuppressWarnings("unchecked")
    public T get(int index) {
        if (index >= 0 && index < count) {
            return (T) items[index];
        }
        return null;
    }
    
    @Override
    public int size() {
        return count;
    }
    
    @Override
    public boolean isEmpty() {
        return count == 0;
    }
}

// 5. GENERIC INTERFACES WITH BOUNDS
interface Comparable<T> {
    int compareTo(T other);
}

interface Repository<T extends Comparable<T>> {
    void save(T item);
    T findMax();
}

class Student implements Comparable<Student> {
    private String name;
    private int grade;
    
    public Student(String name, int grade) {
        this.name = name;
        this.grade = grade;
    }
    
    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.grade, other.grade);
    }
    
    @Override
    public String toString() {
        return name + " (Grade: " + grade + ")";
    }
}

class StudentRepository implements Repository<Student> {
    private MyList<Student> students = new MyList<>();
    
    @Override
    public void save(Student student) {
        students.add(student);
    }
    
    @Override
    public Student findMax() {
        if (students.isEmpty()) return null;
        
        Student max = students.get(0);
        for (int i = 1; i < students.size(); i++) {
            Student current = students.get(i);
            if (current.compareTo(max) > 0) {
                max = current;
            }
        }
        return max;
    }
}

// 6. FUNCTIONAL INTERFACES (for lambdas)
@FunctionalInterface
interface Calculator<T> {
    T calculate(T a, T b);
}

@FunctionalInterface
interface Validator<T> {
    boolean isValid(T item);
}

// 7. DEFAULT AND STATIC METHODS (Java 8+)
interface Logger {
    // Abstract method
    void log(String message);
    
    // Default method - can be overridden
    default void logInfo(String message) {
        log("INFO: " + message);
    }
    
    default void logError(String message) {
        log("ERROR: " + message);
    }
    
    // Static method - belongs to interface
    static String getTimestamp() {
        return "[" + System.currentTimeMillis() + "] ";
    }
}

class ConsoleLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println(Logger.getTimestamp() + message);
    }
    
    // Can override default method if needed
    @Override
    public void logError(String message) {
        System.err.println(Logger.getTimestamp() + "ERROR: " + message);
    }
}
```

________________


### Examples from Design Cache problem

```java
public interface Cache<K, V> {
    V get(K key);
    void put(K key, V value);
    int getCapacity();
    int getCurrentSize();
}

public class InMemoryCache<K, V> implements Cache<K, V> {
    private final Map<K, V> cacheStorage;
    private final int capacity;
    private final EvictionPolicy<K, V> evictionPolicy;

    public InMemoryCache(int capacity, EvictionPolicy<K, V> evictionPolicy) {
        this.capacity = capacity;
        this.cacheStorage = new HashMap<>();
        this.evictionPolicy = evictionPolicy;
    }
    
    @Override
    public V get(K key) {
        evictionPolicy.recordAccess(key);
        return cacheStorage.get(key);
    }
    
    @Override
    public void put(K key, V value) {
        if(cacheStorage.size() >= capacity) {
            K keyToEvict = evictionPolicy.evictKey();
            if (keyToEvict != null) {
                cacheStorage.remove(keyToEvict);
            }
        }
        cacheStorage.put(key, value);
        evictionPolicy.recordAccess(key);
    }

    @Override
    public int getCapacity() {
        return capacity;
    }

    @Override
    public int getCurrentSize() {
        return cacheStorage.size();
    }
}

public interface EvictionPolicy<K,V> {
    K evictKey();
    void recordAccess(K key);
}

// main class
EvictionPolicy<Integer, String> lfuEviction = new LFUEviction<>();
InMemoryCache<Integer, String> cache = new InMemoryCache<>(5, lfuEviction);

if(cache.get(input) == null) {
    String response = api.getResponse(input);
    cache.put(input, response);
    System.out.println("Cache Miss: " + response);
}