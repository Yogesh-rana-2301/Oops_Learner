# 21. Binding in C++

In programming, **binding** refers to the process of associating a function call with the actual code of the function that will be executed. In C++, this can happen at two different times: at compile time or at runtime. This leads to two types of binding: early binding and late binding.

## Early Binding (Static Binding)

Early binding, also known as static binding, occurs at **compile time**. When the compiler encounters a function call, it already knows the exact memory address of the function to be executed. It "binds" the function call to that address directly in the generated machine code.

This is the default binding mechanism in C++ and is very efficient because it involves no runtime overhead.

### When does Early Binding happen?

Early binding is used for:

1.  **Normal function calls.**
2.  **Overloaded function calls.** The compiler resolves which version of the overloaded function to use based on the arguments provided at compile time.
3.  **Calls to non-virtual member functions**, even when using pointers or references.

### Example

```cpp
#include <iostream>

void print(int i) { // An overloaded function
    std::cout << "Integer: " << i << std::endl;
}

void print(double d) { // An overloaded function
    std::cout << "Double: " << d << std::endl;
}

class Base {
public:
    void show() { // A non-virtual function
        std::cout << "Base::show()" << std::endl;
    }
};

class Derived : public Base {
public:
    void show() {
        std::cout << "Derived::show()" << std::endl;
    }
};

int main() {
    // Early binding for overloaded functions
    print(10);   // Compiler binds this to print(int)
    print(5.5);  // Compiler binds this to print(double)

    // Early binding for non-virtual member functions
    Derived d;
    Base* b_ptr = &d;

    d.show();     // Compiler binds this to Derived::show()
    b_ptr->show(); // Compiler binds this to Base::show() because show() is non-virtual
                  // and the pointer type is Base*.

    return 0;
}
```

## Late Binding (Dynamic Binding)

Late binding, also known as dynamic binding, is delayed until **runtime**. The decision of which function to execute is made when the program is actually running, based on the dynamic (actual) type of the object.

This is the mechanism that enables runtime polymorphism in C++.

### When does Late Binding happen?

Late binding occurs when you call a **`virtual` function** through a **base class pointer or reference** that is pointing to a derived class object.

The C++ implementation for late binding is the **v-table** and **v-pointer**. The program uses the object's v-pointer to look up the correct function address in the v-table at runtime.

### Example

```cpp
#include <iostream>

class Base {
public:
    virtual void show() { // A virtual function
        std::cout << "Base::show()" << std::endl;
    }
    virtual ~Base() {}
};

class Derived : public Base {
public:
    void show() override { // Overriding the virtual function
        std::cout << "Derived::show()" << std::endl;
    }
};

int main() {
    Base* b_ptr = new Derived();

    // Late binding for virtual function call
    // At runtime, the program checks the actual type of the object b_ptr points to (which is Derived).
    // It uses the v-table to find and call Derived::show().
    b_ptr->show();

    delete b_ptr;
    return 0;
}
```

**Output:**

```
Derived::show()
```

## Summary: Early vs. Late Binding

| Feature            | Early Binding (Static)                                                | Late Binding (Dynamic)                                                                              |
| :----------------- | :-------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------- |
| **When it Occurs** | Compile time                                                          | Runtime                                                                                             |
| **Mechanism**      | Function address is hard-coded by the compiler.                       | Function address is looked up via the v-table at runtime.                                           |
| **Achieved by**    | Normal functions, overloaded functions, non-virtual member functions. | `virtual` functions called through base class pointers/references.                                  |
| **Performance**    | Faster (no runtime overhead).                                         | Slower (involves v-table lookup), but the overhead is usually minimal.                              |
| **Flexibility**    | Less flexible. The behavior is fixed at compile time.                 | Highly flexible. Allows for polymorphic behavior where the same code can produce different results. |
| **Polymorphism**   | Compile-time polymorphism.                                            | Runtime polymorphism.                                                                               |
