# 11. Inheritance Basics

Inheritance is one of the most important concepts in Object-Oriented Programming (OOP). It is a mechanism that allows a new class (called the **derived class** or **subclass**) to acquire the properties and behaviors (methods and attributes) of an existing class (called the **base class** or **superclass**).

The primary idea behind inheritance is code reusability. You can create a general base class and then create more specific derived classes that reuse the code from the base class and also add their own unique features.

## The "Is-A" Relationship

Inheritance represents an **"is-a" relationship**. This is a fundamental test to determine if inheritance is the correct design choice. If you can say that your derived class "is a" type of your base class, then inheritance is appropriate.

For example:

- A `Car` **is a** `Vehicle`.
- A `Dog` **is a** `Animal`.
- A `Manager` **is a** `Employee`.
- A `CheckingAccount` **is a** `BankAccount`.

This relationship is unidirectional. A `Vehicle` is not necessarily a `Car` (it could be a `Truck`), and an `Animal` is not necessarily a `Dog`.

### Syntax for Inheritance

In C++, you use a colon (`:`) followed by an access specifier (usually `public`) and the name of the base class.

```cpp
class BaseClassName {
    // ... members ...
};

class DerivedClassName : public BaseClassName {
    // ... additional members ...
};
```

### Example: Vehicle and Car

Let's model the "Car is a Vehicle" relationship.

```cpp
#include <iostream>
#include <string>

// Base Class (Superclass)
class Vehicle {
public:
    std::string brand;

    void startEngine() {
        std::cout << "Engine started." << std::endl;
    }
};

// Derived Class (Subclass)
// Car "is-a" Vehicle
class Car : public Vehicle {
public:
    int numberOfDoors;

    void honk() {
        std::cout << "Beep beep!" << std::endl;
    }
};

int main() {
    // Create a Car object
    Car myCar;

    // Access members inherited from Vehicle
    myCar.brand = "Toyota";
    myCar.startEngine(); // Inherited method

    // Access members specific to Car
    myCar.numberOfDoors = 4;
    myCar.honk(); // Car-specific method

    std::cout << "My car is a " << myCar.brand << " with " << myCar.numberOfDoors << " doors." << std::endl;

    return 0;
}
```

In this example:

- The `Vehicle` class has a `brand` and a `startEngine()` method.
- The `Car` class inherits from `Vehicle`. This means a `Car` object automatically has a `brand` attribute and a `startEngine()` method without needing to redefine them.
- The `Car` class also adds its own specific members, `numberOfDoors` and `honk()`.

## Benefits of Inheritance

1.  **Code Reusability:** Avoids duplicating code. Common logic is placed in the base class and reused by all derived classes.
2.  **Extensibility:** You can easily create new classes that extend the functionality of existing ones without modifying the original class.
3.  **Logical Structure:** It helps create a clear and logical hierarchy that mirrors real-world relationships, making the code easier to understand and maintain.
4.  **Polymorphism:** Inheritance is the foundation for polymorphism (the ability to treat objects of different classes through a common interface), which is a powerful OOP concept.
