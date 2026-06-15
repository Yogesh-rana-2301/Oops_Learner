# 2. Classes & Objects

## Class Syntax

A class in C++ is a blueprint for creating objects. It defines a set of attributes (member variables) and methods (member functions) that the objects of the class will have.

The basic syntax for defining a class is:

```cpp
class ClassName {
public:
    // Public members (attributes and methods)
    // Accessible from outside the class

private:
    // Private members (attributes and methods)
    // Accessible only from within the class

protected:
    // Protected members (attributes and methods)
    // Accessible from within the class and by derived classes
};
```

- `class`: The keyword to declare a class.
- `ClassName`: The name of the class.
- `public`, `private`, `protected`: Access specifiers that define the visibility of the members.
  - `public`: Members are accessible from anywhere.
  - `private`: Members are only accessible from within the class itself. This is the <mark>default</mark> access specifier if none is provided.
  - `protected`: Members are accessible within the class and by subclasses (inheritance).

### Example

```cpp
class Car {
public:
    // Attribute
    std::string brand;

    // Method
    void honk() {
        std::cout << "Beep beep!" << std::endl;
    }
};
```

## Object Creation (Stack vs. Heap)

An object is an instance of a class. Once a class is defined, you can create objects from it. There are two primary ways to create objects in C++: on the stack or on the heap.

### Stack Allocation

Objects created on the stack are automatically managed by the compiler. Their memory is allocated when they go into scope and deallocated when they go out of scope. This is the most common and safest way to create objects.

**Syntax:**

```cpp
ClassName objectName;
```

**Example:**

```cpp
#include <iostream>
#include <string>

class Car {
public:
    std::string brand;
};

int main() {
    // Create a Car object on the stack
    Car myCar;
    myCar.brand = "Toyota";

    std::cout << "My car is a " << myCar.brand << std::endl;

    return 0;
} // myCar is automatically destroyed here
```

### Heap Allocation

Objects created on the heap are manually managed by the programmer. You allocate memory for them using the `new` keyword and must deallocate it using the `delete` keyword. Heap allocation is necessary when you need an object to exist beyond the scope in which it was created.

**Syntax:**

```cpp
ClassName* objectPointer = new ClassName();
```

**Example:**

```cpp
#include <iostream>
#include <string>

class Car {
public:
    std::string brand;
};

int main() {
    // Create a Car object on the heap
    Car* myCarPtr = new Car();
    myCarPtr->brand = "Honda"; // Use -> to access members via a pointer

    std::cout << "My car is a " << myCarPtr->brand << std::endl;

    // Manually deallocate the memory
    delete myCarPtr;
    myCarPtr = nullptr; // Good practice to null the pointer after deletion

    return 0;
}
```

**Key Differences:**

| Feature                     | Stack Allocation             | Heap Allocation                                      |
| :-------------------------- | :--------------------------- | :--------------------------------------------------- |
| **Lifetime**                | Automatic (scoped)           | Manual (controlled by `new` and `delete`)            |
| **Memory Management**       | Managed by the compiler      | Managed by the programmer                            |
| **Allocation/Deallocation** | Faster                       | Slower                                               |
| **Risk**                    | Low (no memory leaks)        | High (risk of memory leaks if `delete` is forgotten) |
| **Access**                  | Using the dot operator (`.`) | Using the arrow operator (`->`)                      |

## Memory Layout

When an object is created, memory is allocated to store its member variables. The member functions are not stored with each object; they are stored in a separate memory location and shared among all objects of the same class.

Consider this class:

```cpp
class Student {
public:
    std::string name; // 4 bytes (for pointer, actual string data on heap)
    int age;          // 4 bytes
    double gpa;       // 8 bytes

    void display() {
        // ... function code ...
    }
};
```

When you create a `Student` object:

```cpp
Student s1;
```

The memory allocated for `s1` will be roughly the size of its member variables (`name`, `age`, `gpa`). The `display()` function's code exists in one place in memory, and when you call `s1.display()`, the program jumps to that code, implicitly passing the address of `s1` so the function knows which object's data to operate on (this is where the `this` pointer comes in).

The exact memory layout can be affected by things like data member alignment and padding, which the compiler adds to optimize memory access.


```
Address: 0 1 2 3 4 5 6 7

char c → goes at 0
int x → must start at multiple of 4 → next valid = 4

So bytes 1,2,3 → padding -> pad -> pad-> pad-> 4,5,6, 
```
