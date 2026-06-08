# 29. C++ Edge Cases and Pitfalls

When working with OOP in C++, especially with inheritance and pointers, there are several common pitfalls and edge cases that can lead to subtle bugs or unexpected behavior. Understanding them is crucial for writing robust code.

## 1. Object Slicing

Object slicing occurs when you assign an object of a derived class to an object of a base class. When this happens, the derived part of the object is "sliced off," and only the base class members are copied.

This most often happens when passing a derived object **by value** to a function that expects a base class object.

### Example of Slicing

```cpp
#include <iostream>

class Base {
public:
    virtual void whoAmI() const {
        std::cout << "I am Base" << std::endl;
    }
    virtual ~Base() {}
};

class Derived : public Base {
public:
    void whoAmI() const override {
        std::cout << "I am Derived" << std::endl;
    }
};

// This function takes the object BY VALUE, which causes slicing.
void processObject(Base obj) {
    obj.whoAmI();
}

int main() {
    Derived d;
    std::cout << "Passing Derived object by value:" << std::endl;
    processObject(d); // Slicing happens here!

    return 0;
}
```

**Output:**

```
Passing Derived object by value:
I am Base
```

**What happened?**
When `d` was passed to `processObject`, a new `Base` object `obj` was created on the stack. The `Base` part of `d` was copied into `obj`, but the `Derived` part was lost. The `obj` is a true `Base` object, so calling `obj.whoAmI()` invokes the `Base` version of the function, and all polymorphic behavior is lost.

**How to Avoid Slicing:**
Always pass polymorphic objects **by reference** or **by pointer**.

```cpp
// Correct way: pass by reference
void processObjectCorrectly(const Base& obj) {
    obj.whoAmI(); // No slicing, polymorphism works
}

int main() {
    Derived d;
    processObjectCorrectly(d); // Prints "I am Derived"
    return 0;
}
```

## 2. Dangling Pointers

A dangling pointer is a pointer that points to a memory location that has already been deallocated (freed) or is no longer valid. Using a dangling pointer leads to undefined behavior, which often results in a program crash.

### Common Causes

1.  **Accessing Memory After `delete`:**

    ```cpp
    int* ptr = new int(10);
    delete ptr;
    // ptr is now a dangling pointer.
    *ptr = 20; // UNDEFINED BEHAVIOR!
    ```

    **Fix:** Set the pointer to `nullptr` immediately after deleting it. This prevents accidental usage.

    ```cpp
    delete ptr;
    ptr = nullptr;
    ```

2.  **Returning a Pointer to a Local Variable:**
    A function's local variables exist on the stack and are destroyed when the function returns. Returning a pointer to a local variable creates a dangling pointer.

    ```cpp
    int* createDanglingPointer() {
        int local_var = 42;
        return &local_var; // Returning address of a local variable
    } // local_var is destroyed here.

    int main() {
        int* dangling_ptr = createDanglingPointer();
        // dangling_ptr points to invalid memory.
        std::cout << *dangling_ptr << std::endl; // UNDEFINED BEHAVIOR!
    }
    ```

    **Fix:** Allocate the memory on the heap using `new` if it needs to outlive the function, or use smart pointers.

3.  **Shallow Copy Issues:** As seen in the Copy Constructor topic, shallow copies can lead to one object's destructor freeing memory that another object's pointer still points to, creating a dangling pointer.

### Preventing Dangling Pointers

- Initialize pointers to `nullptr`.
- Set pointers to `nullptr` after `delete`.
- Never return pointers or references to local variables.
- Use **smart pointers** (`std::unique_ptr`, `std::shared_ptr`) to automate memory management. This is the best and most modern approach.
