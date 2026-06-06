# 14. Constructor and Destructor Order in Inheritance

When you create an object of a derived class, the constructors of both the base class and the derived class are called. Similarly, when the object is destroyed, the destructors of both are called. The order in which these calls happen is fixed and logical.

## Constructor Execution Order: Base → Derived

When a derived class object is created, the constructor execution follows a specific order:

1.  **Base Class Constructor:** The base class constructor is called **first**.
2.  **Derived Class Constructor:** The derived class constructor is called **second**.

The logic behind this is that the derived class "is-a" base class. The base part of the object must be fully constructed and ready before the derived class can add its own features on top of it.

### How to Call a Base Class Constructor

If the base class has a default (no-argument) constructor, it will be called automatically. However, if you need to call a parameterized constructor of the base class, you **must** do so explicitly in the derived class's constructor using the **member initializer list**.

**Syntax:**

```cpp
Derived::Derived(args) : Base(base_args) {
    // ... derived constructor body ...
}
```

### Example

```cpp
#include <iostream>
#include <string>

class Base {
public:
    Base() {
        std::cout << "Base default constructor called." << std::endl;
    }
    Base(std::string msg) {
        std::cout << "Base parameterized constructor called with: " << msg << std::endl;
    }
};

class Derived : public Base {
public:
    // This constructor will implicitly call Base's default constructor
    Derived() {
        std::cout << "Derived default constructor called." << std::endl;
    }

    // This constructor explicitly calls Base's parameterized constructor
    Derived(std::string msg) : Base(msg) {
        std::cout << "Derived parameterized constructor called." << std::endl;
    }
};

int main() {
    std::cout << "--- Creating d1 ---" << std::endl;
    Derived d1; // Calls Base() then Derived()

    std::cout << "\n--- Creating d2 ---" << std::endl;
    Derived d2("Hello from Derived"); // Calls Base(msg) then Derived(msg)

    std::cout << "\n--- End of main ---" << std::endl;
    return 0;
}
```

**Output:**

```
--- Creating d1 ---
Base default constructor called.
Derived default constructor called.

--- Creating d2 ---
Base parameterized constructor called with: Hello from Derived
Derived parameterized constructor called.

--- End of main ---
```

## Destructor Execution Order: Derived → Base

The order of destructor calls is the **exact reverse** of the constructor calls.

1.  **Derived Class Destructor:** The derived class destructor is called **first**.
2.  **Base Class Destructor:** The base class destructor is called **second**.

The logic here is that the derived part of the object must be cleaned up first before the base part, which it depends on, is destroyed.

Destructors do not have parameters, so the chain of calls happens automatically. You do not need to (and cannot) explicitly call the base class destructor.

### Example

```cpp
#include <iostream>

class Base {
public:
    ~Base() {
        std::cout << "Base destructor called." << std::endl;
    }
};

class Derived : public Base {
public:
    ~Derived() {
        std::cout << "Derived destructor called." << std::endl;
    }
};

int main() {
    std::cout << "Creating Derived object..." << std::endl;
    Derived d;
    std::cout << "Derived object created. Program ending..." << std::endl;
    return 0;
} // d goes out of scope here
```

**Output:**

```
Creating Derived object...
Derived object created. Program ending...
Derived destructor called.
Base destructor called.
```

**Important Note:** For this automatic destructor chaining to work correctly with polymorphism (i.e., when deleting a derived object through a base class pointer), the base class destructor **must** be declared `virtual`.
