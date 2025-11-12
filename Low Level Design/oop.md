## Basics of OOP

1. Encapsulation - Bundle data and methods together, control access
2. Inheritance - Create new classes based on existing ones
3. Polymorphism - Same method name, different implementations
4. Abstraction - Hide complex implementation details

Encapsulation: name is private, accessed via getName()
Inheritance: Dog and Cat inherit from Animal
Polymorphism: makeSound() behaves differently for each animal
Abstraction: introduce() hides the complexity of calling multiple methods


```java
// Base class demonstrating Encapsulation
class Animal {
    private String name;    // Private data (encapsulated)
    protected int age;      // Protected for inheritance
    
    // Constructor
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Getter method (controlled access)
    public String getName() {
        return name;
    }
    
    // Method to be overridden (Polymorphism)
    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
    
    // Method showing Abstraction
    public void introduce() {
        System.out.println("I'm " + name + ", age " + age);
        makeSound();
    }
}

// Inheritance - Dog extends Animal
class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age);    // Call parent constructor
        this.breed = breed;
    }
    
    // Polymorphism - Override parent method
    @Override
    public void makeSound() {
        System.out.println(getName() + " barks: Woof!");
    }
    
    // Dog-specific method
    public void wagTail() {
        System.out.println(getName() + " wags tail happily");
    }
}

// Another inheritance example
class Cat extends Animal {
    public Cat(String name, int age) {
        super(name, age);
    }
    
    // Polymorphism - Different implementation
    @Override
    public void makeSound() {
        System.out.println(getName() + " meows: Meow!");
    }
}

// Main class to demonstrate
public class OOPDemo {
    public static void main(String[] args) {
        // Creating objects
        Dog dog = new Dog("Buddy", 3, "Golden Retriever");
        Cat cat = new Cat("Whiskers", 2);
        
        // Encapsulation in action
        System.out.println("Dog's name: " + dog.getName());
        
        // Polymorphism - same method, different behavior
        dog.introduce();    // Calls Dog's makeSound()
        cat.introduce();    // Calls Cat's makeSound()
        
        // Inheritance - Dog has Animal methods + its own
        dog.wagTail();
        
        // Polymorphism with array
        Animal[] animals = {dog, cat};
        System.out.println("\nPolymorphism demo:");
        for (Animal animal : animals) {
            animal.makeSound();  // Calls appropriate version
        }
    }
}
```


## Access Modifiers

### **Protected**

The protected category is unique. The access level to the protected members lies somewhere between private and public. The primary use of the protected tag can be found when using **inheritance**, which is the process of creating classes out of other classes.

The protected data members can be accessed inside a Java package. However, outside the package, they can only be referred to through an inherited class.

## **Static and non-static fields**

Java supports static and non-static fields.

### **Static field**

A static field resides in a class. All the objects we create will share this field and its value.

Static fields reside in the class. We don’t need an instance of the class to access static fields. We can access the static fields of a class by just writing the class name before the field.

```java
class Car {
    // static fields
    static int topSpeed = 100;
    static int maxCapacity = 4;  
}

class Demo {
    public static void main(String args[]){
    // Static fields are accessible in the main
    System.out.println(Car.topSpeed);
    System.out.println(Car.maxCapacity);   
  }
}
```

### **Non-static field**

Non-static fields are located in the instances of the class. Each instance of the class can have its own values for these fields.

```java
class Car {
    // static fields
    int speed = 100;
    int capacity = 4;  
}

class Demo {
    public static void main(String args[]){
    Car obj1 = new Car();
    System.out.println(obj1.speed);
    System.out.println(obj1.capacity);   
  }
}
```

### **Final fields**

A final field cannot have its value changed once it is assigned. We can make a field final by using the keyword `final`

```java
class Car {
  // Final variable capacity
  final int capacity = 4;
}

class Demo {
   public static void main(String args[]) {
      Car car = new Car();
      car.capacity = 5; // Trying to change the capacity value
   }
}
```
