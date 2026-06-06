# 19. Virtual Functions

A virtual function is a member function in a base class that you expect to be redefined (overridden) in derived classes. It is the C++ mechanism for enabling **late binding** and **runtime polymorphism**.

When you have a pointer or reference to a base class that actually points to a derived class object, calling a virtual function on that pointer/reference will execute the derived class's version of the function.

You declare a function as virtual by using the `virtual` keyword in front of its declaration in the base class.

```cpp
class Base {
public:
    virtual void myFunction();
};
```

## The V-Table (Virtual Table) Concept

To implement this runtime behavior, C++ compilers typically use a mechanism called a **virtual table** or **v-table**.

1.  **What is a V-Table?**
    A v-table is a static array of function pointers. Every class that has at least one virtual function (or inherits one) has its own v-table. This table contains the memory addresses of the virtual functions for that specific class.

2.  **What is a V-Pointer (VPTR)?**
    When an object of a class with virtual functions is created, the compiler adds a hidden member to the object: a pointer called the **virtual pointer** or **v-pointer (vptr)**. This vptr points to the v-table for that class.

### How it Works at Runtime

Let's trace a call using our `Animal` example:

```cpp
class Animal {
public:
    virtual void makeSound() const { /* ... */ }
};

class Dog : public Animal {
public:
    void makeSound() const override { /* ... */ }
};

int main() {
    Animal* ptr = new Dog(); // Base pointer, derived object
    ptr->makeSound();        // How does this call Dog::makeSound()?
    delete ptr;
}
```

Here's the step-by-step process for the call `ptr->makeSound()`:

1.  **Dereference the V-Pointer:** The program follows the `ptr` to the `Dog` object in memory. It then finds the hidden v-pointer inside that `Dog` object.

2.  **Look up the V-Table:** The program follows the v-pointer to the `Dog` class's v-table.

3.  **Find the Function Address:** The compiler knows that `makeSound()` is the first virtual function (or the Nth). It looks up the corresponding entry (e.g., the first entry) in the `Dog` v-table to get the memory address of a function.

4.  **Call the Function:** The program calls the function at that address. Since the v-pointer pointed to the `Dog` v-table, the address found is that of `Dog::makeSound()`.

This lookup process (dereferencing the vptr and indexing the v-table) is what constitutes **late binding** or **dynamic dispatch**. It happens every time a virtual function is called through a base class pointer or reference.

### Visual Representation

```
// In Memory

// Dog Object
+-----------------+
| vptr            | ----> // Dog Class V-Table
| (other members) |     +--------------------------+
+-----------------+     | &Dog::makeSound          | (Address of Dog's function)
                      +--------------------------+
                      | (address of other virtuals)|
                      +--------------------------+

// Animal Object
+-----------------+
| vptr            | ----> // Animal Class V-Table
| (other members) |     +--------------------------+
+-----------------+     | &Animal::makeSound       | (Address of Animal's function)
                      +--------------------------+
                      | (address of other virtuals)|
                      +--------------------------+
```

## Key Characteristics of Virtual Functions

- **Inheritance:** If a function is declared `virtual` in a base class, it remains `virtual` in all its derived classes, even if the `virtual` keyword is not used again.
- **Performance:** There is a slight performance overhead for calling a virtual function compared to a non-virtual function due to the v-table lookup. However, this is usually negligible and is the price for the flexibility of runtime polymorphism.
- **Constructors:** Constructors cannot be `virtual`.
- **Destructors:** Destructors **should almost always be `virtual`** in a base class if you expect to delete a derived object through a base class pointer. This ensures that the correct chain of destructors (derived then base) is called, preventing resource leaks.

```cpp
class Base {
public:
    virtual ~Base() { /* ... */ } // Virtual destructor
};
```

Virtual functions are the cornerstone of polymorphic behavior in C++, enabling the creation of flexible and extensible systems where code can operate on objects of different types through a common interface.
