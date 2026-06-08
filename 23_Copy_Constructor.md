# 23. Copy Constructor

A copy constructor is a special constructor that creates a new object as an initialized copy of an existing object of the same class. It's a fundamental part of controlling how your objects are copied.

The syntax for a copy constructor is:
`ClassName(const ClassName& other);`

- `const`: The source object (`other`) is passed as a `const` reference because the copy operation should not modify the original object.
- `&`: It's passed by reference to avoid making another copy. If it were passed by value, it would require a call to the copy constructor to make the copy, leading to an infinite recursive loop.

If you don't provide a copy constructor, the C++ compiler generates a default one for you.

## Shallow Copy vs. Deep Copy

The distinction between shallow and deep copies is critical when your class manages resources like dynamic memory (raw pointers).

### Shallow Copy (The Default Behavior)

The compiler-generated copy constructor performs a **shallow copy**. This means it copies the value of each member variable from the original object to the new object, bit by bit.

- For simple data types (`int`, `double`, etc.), this is fine.
- For pointers, **only the pointer's address is copied**, not the data it points to.

This results in two objects whose pointers point to the **same memory location**.

**Problem with Shallow Copy:**

1.  **Dangling Pointers:** When one object is destroyed, its destructor frees the shared memory. The other object is now left with a dangling pointer to memory that is no longer valid.
2.  **Double Free:** When the second object is destroyed, its destructor tries to free the _same memory again_, leading to a crash or undefined behavior.
3.  **Unintended Side Effects:** A change made to the pointed-to data through one object will affect the other object.

#### Example of Shallow Copy Problem

```cpp
#include <iostream>

class ShallowBox {
public:
    int* data;
    ShallowBox(int val) {
        data = new int(val);
        std::cout << "Constructor allocating memory." << std::endl;
    }
    ~ShallowBox() {
        std::cout << "Destructor freeing memory." << std::endl;
        delete data;
    }
    // No user-defined copy constructor, so compiler provides a shallow copy one.
};

int main() {
    ShallowBox box1(100);
    {
        ShallowBox box2 = box1; // Shallow copy
        // Now box1.data and box2.data point to the same memory.
    } // box2 is destroyed here, its destructor frees the memory.

    // Now box1.data is a dangling pointer.
    // The program will crash when main ends and box1 is destroyed,
    // because it will try to delete the same memory again.
    return 0;
}
```

### Deep Copy (The Correct Approach for Pointers)

To solve this, you must implement your own copy constructor that performs a **deep copy**.

A deep copy involves:

1.  Allocating **new memory** for the copied object.
2.  Copying the **actual data** from the original memory location to the new memory location.

This ensures that the original and copied objects are fully independent.

#### Example of Deep Copy

```cpp
#include <iostream>

class DeepBox {
public:
    int* data;
    DeepBox(int val) {
        data = new int(val);
    }

    // User-defined copy constructor for deep copy
    DeepBox(const DeepBox& other) {
        std::cout << "Deep copy constructor called." << std::endl;
        // 1. Allocate new memory
        data = new int;
        // 2. Copy the data, not the pointer
        *data = *other.data;
    }

    ~DeepBox() {
        delete data;
    }
};

int main() {
    DeepBox box1(100);
    DeepBox box2 = box1; // Deep copy is performed.

    // box1 and box2 are now independent.
    *box1.data = 200;

    std::cout << "box1 data: " << *box1.data << std::endl; // 200
    std::cout << "box2 data: " << *box2.data << std::endl; // 100 (unaffected)

    return 0; // No crash, as each object manages its own memory.
}
```

### When is the Copy Constructor Called?

The copy constructor is invoked in the following situations:

1.  **Initialization:** When a new object is initialized from an existing object of the same class.
    ```cpp
    Widget w1;
    Widget w2 = w1; // Copy constructor
    Widget w3(w1);  // Copy constructor (alternative syntax)
    ```
2.  **Pass by Value:** When an object is passed to a function by value.
    ```cpp
    void myFunction(Widget w);
    Widget w1;
    myFunction(w1); // Copy constructor called to create 'w'
    ```
3.  **Return by Value:** When an object is returned from a function by value.
    `cpp
    Widget createWidget() {
        Widget w;
        return w; // Copy constructor may be called here (compiler can optimize this away)
    }
    `
    This is why the **Rule of Three/Five** is so important: if you are managing resources that require a destructor, you almost certainly need to control copying as well.
