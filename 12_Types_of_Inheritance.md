# 12. Types of Inheritance

In C++, inheritance can be structured in several ways, depending on how many base and derived classes are involved. The primary types of inheritance are:

1.  Single Inheritance
2.  Multiple Inheritance
3.  Multilevel Inheritance
4.  Hierarchical Inheritance
5.  Hybrid Inheritance (a combination of the others)

## 1. Single Inheritance

This is the simplest form of inheritance, where a derived class inherits from only **one** base class. This is the most common type of inheritance.

**Structure:**

```
      A (Base)
      |
      B (Derived)
```

### Example

```cpp
#include <iostream>

class Animal { // Base class
public:
    void eat() {
        std::cout << "Eating..." << std::endl;
    }
};

class Dog : public Animal { // Derived class
public:
    void bark() {
        std::cout << "Barking..." << std::endl;
    }
};

int main() {
    Dog myDog;
    myDog.eat();  // Inherited from Animal
    myDog.bark(); // Defined in Dog
    return 0;
}
```

## 2. Multiple Inheritance

In multiple inheritance, a single derived class inherits from **two or more** base classes. This allows the derived class to combine the features of several existing classes.

**Structure:**

```
  A (Base)   B (Base)
   \         /
    \       /
      C (Derived)
```

### Example

```cpp
#include <iostream>

class Camera {
public:
    void takePhoto() {
        std::cout << "Taking a photo..." << std::endl;
    }
};

class Phone {
public:
    void makeCall() {
        std::cout << "Making a call..." << std::endl;
    }
};

// SmartPhone inherits from both Camera and Phone
class SmartPhone : public Camera, public Phone {
public:
    void browseInternet() {
        std::cout << "Browsing the internet..." << std::endl;
    }
};

int main() {
    SmartPhone myPhone;
    myPhone.takePhoto();    // Inherited from Camera
    myPhone.makeCall();     // Inherited from Phone
    myPhone.browseInternet(); // Defined in SmartPhone
    return 0;
}
```

_Note: Multiple inheritance can be complex and can lead to issues like the "Diamond Problem," which requires careful management._

## 3. Multilevel Inheritance

In multilevel inheritance, a derived class becomes the base class for another class. This creates a chain of inheritance.

**Structure:**

```
      A (Base)
      |
      B (Intermediate Derived)
      |
      C (Leaf Derived)
```

### Example

```cpp
#include <iostream>

class Grandparent {
public:
    void sleep() {
        std::cout << "Sleeping..." << std::endl;
    }
};

class Parent : public Grandparent { // Inherits from Grandparent
public:
    void work() {
        std::cout << "Working..." << std::endl;
    }
};

class Child : public Parent { // Inherits from Parent
public:
    void play() {
        std::cout << "Playing..." << std::endl;
    }
};

int main() {
    Child myChild;
    myChild.sleep(); // Inherited from Grandparent
    myChild.work();  // Inherited from Parent
    myChild.play();  // Defined in Child
    return 0;
}
```

## 4. Hierarchical Inheritance

In hierarchical inheritance, **multiple** derived classes inherit from a **single** base class. This is useful when you have a general category and several specific types that fall under it.

**Structure:**

```
      A (Base)
     /       \
    /         \
  B (Derived)  C (Derived)
```

### Example

```cpp
#include <iostream>

class Shape { // Base class
public:
    void setSize(int s) { size = s; }
protected:
    int size;
};

class Circle : public Shape { // Derived class 1
public:
    void draw() { std::cout << "Drawing a circle of size " << size << std::endl; }
};

class Square : public Shape { // Derived class 2
public:
    void draw() { std::cout << "Drawing a square of size " << size << std::endl; }
};

int main() {
    Circle c;
    c.setSize(10);
    c.draw();

    Square s;
    s.setSize(5);
    s.draw();
    return 0;
}
```

## 5. Hybrid Inheritance

Hybrid inheritance (or virtual inheritance) is a combination of two or more of the above types of inheritance. For example, combining hierarchical and multiple inheritance can lead to the Diamond Problem, which is often solved using `virtual` inheritance.
