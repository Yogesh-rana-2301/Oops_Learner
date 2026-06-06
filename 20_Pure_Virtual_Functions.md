# 20. Pure Virtual Functions and Abstract Classes

## Pure Virtual Function

A pure virtual function is a virtual function in a base class that has no implementation. It acts as a placeholder and enforces a rule that any non-abstract derived class **must** provide an implementation for it.

You declare a pure virtual function by assigning `= 0` to its declaration.

**Syntax:**

```cpp
class Base {
public:
    // A pure virtual function
    virtual void myFunction() = 0;
};
```

The `= 0` syntax does not mean assigning zero to the function pointer. It is simply the C++ notation to mark a virtual function as "pure."

## Abstract Class

An **abstract class** is a class that has at least one pure virtual function.

Key properties of an abstract class:

1.  **Cannot be Instantiated:** You cannot create an object of an abstract class. The compiler will prevent you from doing so because the class is considered "incomplete" (it has a function without a body).

    ```cpp
    // Assuming 'Shape' is an abstract class
    Shape myShape; // COMPILE ERROR!
    ```

2.  **Designed for Inheritance:** Abstract classes are designed exclusively to be used as base classes in an inheritance hierarchy.

3.  **Enforces an Interface:** An abstract class defines a common **interface** that all its concrete (non-abstract) derived classes must adhere to. It guarantees that any object of a derived class will have an implementation for the pure virtual functions defined in the base.

### Example: A `Shape` Abstract Class

Let's create a `Shape` hierarchy. It doesn't make sense to calculate the area of a generic "shape," but we know that any concrete shape (like a circle or a square) must have a way to calculate its area.

```cpp
#include <iostream>

// Abstract Base Class (ABC)
class Shape {
public:
    // Pure virtual function - defines an interface
    virtual double getArea() const = 0;

    // A virtual destructor is crucial for a polymorphic base class
    virtual ~Shape() {}
};

// Concrete Derived Class 1
class Circle : public Shape {
private:
    double radius;
public:
    Circle(double r) : radius(r) {}

    // Provide the mandatory implementation for the pure virtual function
    double getArea() const override {
        return 3.14159 * radius * radius;
    }
};

// Concrete Derived Class 2
class Rectangle : public Shape {
private:
    double width, height;
public:
    Rectangle(double w, double h) : width(w), height(h) {}

    // Provide the mandatory implementation
    double getArea() const override {
        return width * height;
    }
};

// This function can work with any class derived from Shape
void printArea(const Shape& shape) {
    std::cout << "The area is: " << shape.getArea() << std::endl;
}

int main() {
    // Shape s; // ERROR: Cannot instantiate abstract class 'Shape'

    Circle c(10.0);
    Rectangle r(5.0, 4.0);

    printArea(c); // Works with a Circle object
    printArea(r); // Works with a Rectangle object

    return 0;
}
```

**Output:**

```
The area is: 314.159
The area is: 20
```

In this example:

- `Shape` is an abstract class because `getArea()` is a pure virtual function.
- `Circle` and `Rectangle` are concrete classes because they provide an implementation for `getArea()`.
- The `printArea` function can confidently call `shape.getArea()` because the abstract base class `Shape` guarantees that any concrete object passed to it will have that function implemented.

## Abstract Classes vs. Interfaces

In C++, an abstract class is the primary way to create an **interface**. An interface is a class that has no implementation and no data members, only pure virtual functions. It defines a contract that other classes can agree to fulfill.

A "pure" interface in C++ would look like this:

```cpp
class ISerializable {
public:
    virtual void serialize(std::ostream& stream) = 0;
    virtual void deserialize(std::istream& stream) = 0;
    virtual ~ISerializable() {} // Virtual destructor is still important
};
```

Any class that needs to be serializable can inherit from `ISerializable` and implement the two methods. This provides a powerful way to design loosely coupled systems.
