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
