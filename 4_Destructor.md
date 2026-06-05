# 4. Destructor

A destructor is a special member function of a class that is executed whenever an object of its class goes out of scope or is explicitly deleted. Its main purpose is to "clean up" and release resources that the object may have acquired during its lifetime.

Key properties of a destructor:

- It has the same name as the class, preceded by a tilde (`~`).
- It does not have a return type (not even `void`).
- It cannot take any arguments (and therefore cannot be overloaded).
- A class can only have one destructor.

### When is a Destructor Called?

A destructor is called automatically in the following situations:

1.  **End of Scope:** When an object created on the stack goes out of scope.
2.  **`delete` is Called:** When an object created on the heap is explicitly deallocated using `delete`.
3.  **Container Destruction:** When an object is an element of a container (like a `std::vector` or `std::list`), and the container is destroyed.
4.  **Program Termination:** For global or static objects, the destructor is called when the program finishes.

### Example: Basic Destructor

```cpp
#include <iostream>

class MyClass {
public:
    MyClass() {
        std::cout << "Constructor called." << std::endl;
    }

    ~MyClass() {
        std::cout << "Destructor called." << std::endl;
    }
};

void createObject() {
    std::cout << "Entering createObject function." << std::endl;
    MyClass obj; // Object created on the stack
    std::cout << "Exiting createObject function." << std::endl;
} // obj goes out of scope here, destructor is called

int main() {
    createObject();
    return 0;
}
```

**Output:**

```
Entering createObject function.
Constructor called.
Exiting createObject function.
Destructor called.
```

## Stack vs. Heap Destruction

The way a destructor is invoked depends on whether the object was allocated on the stack or the heap.

### Stack Object Destruction

Objects created on the stack are automatically managed. Their destructors are called as soon as they go out of scope. This is known as **RAII (Resource Acquisition Is Initialization)**, a core concept in C++. The destruction happens in the reverse order of construction.

```cpp
#include <iostream>

class Simple {
public:
    int id;
    Simple(int i) : id(i) { std::cout << "Constructing Simple " << id << std::endl; }
    ~Simple() { std::cout << "Destructing Simple " << id << std::endl; }
};

int main() {
    Simple s1(1);
    if (true) {
        Simple s2(2);
    } // s2 is destructed here
    Simple s3(3);
    return 0;
} // s3 is destructed, then s1 is destructed
```

**Output:**

```
Constructing Simple 1
Constructing Simple 2
Destructing Simple 2
Constructing Simple 3
Destructing Simple 3
Destructing Simple 1
```

### Heap Object Destruction

Objects created on the heap with `new` exist until they are explicitly deallocated with `delete`. The destructor is **only called when `delete` is used**.

Forgetting to call `delete` on a heap-allocated object results in a **memory leak**. The memory occupied by the object is never released, and its destructor is never called, which can also lead to a **resource leak** if the object was managing other resources (like file handles or network connections).

```cpp
#include <iostream>

class ResourceHolder {
private:
    int* data;
public:
    ResourceHolder() {
        data = new int[100]; // Acquire a resource (memory)
        std::cout << "Resource acquired." << std::endl;
    }

    ~ResourceHolder() {
        delete[] data; // Release the resource
        std::cout << "Resource released." << std::endl;
    }
};

int main() {
    std::cout << "Creating object on the heap." << std::endl;
    ResourceHolder* rhPtr = new ResourceHolder();

    // ... use the object ...

    std::cout << "Deleting object from the heap." << std::endl;
    delete rhPtr; // Destructor is called here

    return 0;
}
```

**Output:**

```
Creating object on the heap.
Resource acquired.
Deleting object from the heap.
Resource released.
```

If you were to comment out `delete rhPtr;`, the "Resource released." message would never be printed, and the memory for `data` would be leaked. This is why smart pointers (`std::unique_ptr`, `std::shared_ptr`) are heavily recommended for managing heap-allocated objects, as they automate the call to `delete`.

## Destructors and Polymorphism (Virtual Destructors)

A critical issue arises when you delete a dynamically allocated object through a pointer to a base class. If the base class destructor is not `virtual`, only the base class destructor will be called, even if the pointer is actually pointing to a derived class object. This leads to the derived class's resources not being cleaned up, causing resource leaks and undefined behavior.

**Rule of Thumb:** If a class is intended to be a base class in a polymorphic hierarchy, its destructor **must** be declared `virtual`.

### Example: The Problem without a Virtual Destructor

```cpp
#include <iostream>

class Base {
public:
    Base() { std::cout << "Base Constructor\n"; }
    // Non-virtual destructor
    ~Base() { std::cout << "Base Destructor\n"; }
};

class Derived : public Base {
private:
    int* _data;
public:
    Derived() {
        _data = new int[100];
        std::cout << "Derived Constructor (resources allocated)\n";
    }
    ~Derived() {
        delete[] _data;
        std::cout << "Derived Destructor (resources released)\n";
    }
};

int main() {
    Base* b = new Derived();
    // b is a Base pointer, but it points to a Derived object

    delete b; // Problem here!

    return 0;
}
```

**Incorrect Output:**

```
Base Constructor
Derived Constructor (resources allocated)
Base Destructor
```

Notice that the `Derived` destructor was never called. The memory allocated for `_data` is now leaked.

### Example: The Solution with a Virtual Destructor

By simply adding the `virtual` keyword to the base class destructor, we ensure that the correct destructor chain is called.

```cpp
#include <iostream>

class Base {
public:
    Base() { std::cout << "Base Constructor\n"; }
    // Virtual destructor
    virtual ~Base() { std::cout << "Base Destructor\n"; }
};

class Derived : public Base {
private:
    int* _data;
public:
    Derived() {
        _data = new int[100];
        std::cout << "Derived Constructor (resources allocated)\n";
    }
    ~Derived() {
        delete[] _data;
        std::cout << "Derived Destructor (resources released)\n";
    }
};

int main() {
    Base* b = new Derived();
    delete b; // Works correctly now
    return 0;
}
```

**Correct Output:**

```
Base Constructor
Derived Constructor (resources allocated)
Derived Destructor (resources released)
Base Destructor
```

With `virtual ~Base()`, when `delete b` is called, the program sees that `b` points to a `Derived` object and calls the `Derived` destructor first. The `Derived` destructor then automatically calls the `Base` destructor, ensuring that all resources are cleaned up correctly in the reverse order of construction.
