# 5. `this` Pointer

In C++, `this` is a keyword that represents a pointer to the current object instance. It is an implicit parameter to all non-static member functions.

When you call a member function on an object, the compiler secretly passes the address of that object to the function as the `this` pointer. This is how the member function knows which object's data to operate on.

## Internal Working

Let's consider a simple class:

```cpp
class Simple {
public:
    int data;

    void setData(int value) {
        data = value;
    }
};
```

When you write this code:

```cpp
Simple obj1;
obj1.setData(10);

Simple obj2;
obj2.setData(20);
```

The compiler translates the member function calls into something like this:

```cpp
// Pseudo-code of what the compiler does
Simple obj1;
Simple_setData(&obj1, 10); // Pass the address of obj1

Simple obj2;
Simple_setData(&obj2, 20); // Pass the address of obj2
```

And the `setData` member function is internally defined like this:

```cpp
// Pseudo-code of the member function
void Simple_setData(Simple* const this, int value) {
    this->data = value; // Use the 'this' pointer to access the member
}
```

- The `this` pointer is a `const` pointer, meaning you cannot change the address it holds (you can't make it point to a different object), but you can modify the data of the object it points to.
- The type of `this` in a member function of class `X` is `X*`. If the member function is `const`, the type of `this` is `const X*`.

## How `this` References the Object

The `this` pointer is used to access the members of the object for which the member function is called.

### 1. Disambiguating Member Variables and Parameters

A common use case for `this` is to resolve ambiguity when a function parameter has the same name as a member variable.

```cpp
#include <iostream>

class Box {
public:
    int width;

    // Parameter 'width' has the same name as member 'width'
    void setWidth(int width) {
        // 'this->width' refers to the member variable
        // 'width' refers to the local parameter
        this->width = width;
    }
};

int main() {
    Box b;
    b.setWidth(10);
    std::cout << "Box width: " << b.width << std::endl; // Prints 10
    return 0;
}
```

Without `this->`, the line `width = width;` would simply assign the parameter to itself, leaving the member variable unchanged.

### 2. Returning the Current Object

The `this` pointer can be used to return a reference to the current object from a member function. This is essential for enabling method chaining.

```cpp
#include <iostream>

class Calc {
private:
    int value;
public:
    Calc() : value(0) {}

    Calc& add(int num) {
        value += num;
        return *this; // Return a reference to the current object
    }

    Calc& subtract(int num) {
        value -= num;
        return *this; // Return a reference to the current object
    }

    void print() {
        std::cout << "Value: " << value << std::endl;
    }
};

int main() {
    Calc c;

    // Method chaining
    c.add(10).subtract(3).add(5);

    c.print(); // Prints "Value: 12"

    return 0;
}
```

In this example, `add` and `subtract` return `*this` (the object itself, by reference). This allows the result of one call (the object) to be used for the next call in the chain.

### 3. Passing the Current Object to Another Function

Sometimes a member function needs to pass the current object to another function that expects an object or a pointer to an object.

```cpp
void someGlobalFunction(MyClass* objPtr) {
    // ... do something with the object ...
}

class MyClass {
public:
    void doSomething() {
        // Pass a pointer to the current object to the global function
        someGlobalFunction(this);
    }
};
```


> Extra
```cpp

#include <iostream>
using namespace std;

class MyClass;

// Global function (takes pointer)
void globalWithPointer(MyClass* obj);

// Global function (takes object reference)
void globalWithObject(MyClass& obj);

class MyClass {
private:
    int value;

public:
    // Constructor
    MyClass(int v) : value(v) {}

    // Member function
    void show() {
        cout << "Value: " << value << endl;
    }

    void doSomething() {
        cout << "\n--- Inside doSomething ---\n";

        // this = pointer to current object
        cout << "this (address): " << this << endl;

        // *this = actual object
        cout << "*this (value): ";
        this->show();

        // Passing pointer
        globalWithPointer(this);

        // Passing object
        globalWithObject(*this);
    }

    // Modify value using pointer
    void increment() {
        value++;
    }
};

// Global function using pointer
void globalWithPointer(MyClass* obj) {
    cout << "\n[Pointer Function]\n";
    cout << "Address received: " << obj << endl;

    // Access using ->
    obj->increment();
    obj->show();
}

// Global function using reference (object)
void globalWithObject(MyClass& obj) {
    cout << "\n[Object Function]\n";

    // Access using .
    obj.increment();
    obj.show();
}

int main() {
    MyClass obj(10);

    cout << "--- Initial State ---\n";
    obj.show();

    obj.doSomething();

    cout << "\n--- Final State ---\n";
    obj.show();

    return 0;
}

```
