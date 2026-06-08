:white_check_mark: __kritsnasya__ 06/06/2026

# 10. Static Members

In C++, the `static` keyword is used to create members (both variables and functions) that belong to the class itself, rather than to any specific object instance. This means there is only one copy of a static member, which is shared by all objects of that class.

## Static Member Variables

A static member variable (or static data member) is a variable that is shared among all instances of a class. It is initialized only once and exists for the entire lifetime of the program.

**Key Properties:**

- **Shared:** All objects of the class share the same static variable. If one object changes it, the change is visible to all other objects.
- **Class Scope:** It is part of the class, but it is not part of any individual object. `sizeof(object)` does not include the size of static members.
- **Initialization:** It must be defined and initialized outside the class, usually in the corresponding `.cpp` file. This allocates storage for it.

### Example

Static variables are often used to keep track of information that is common to all objects, like counting the number of objects created.

```cpp
// Thing.h
#include <iostream>

class Thing {
public:
    // A regular member variable - each object gets its own
    int id;

    // A static member variable - shared by all objects
    static int count;

    Thing() {
        // Use the shared count to assign a unique ID
        id = ++count;
        std::cout << "Thing " << id << " created." << std::endl;
    }

    ~Thing() {
        std::cout << "Thing " << id << " destroyed." << std::endl;
        // Note: We don't typically decrement the count in the destructor
        // as it represents the total number ever created.
    }
};

// Thing.cpp
// #include "Thing.h"

// Definition and initialization of the static member variable
int Thing::count = 0;


// Main.cpp
int main() {
    std::cout << "Initial count: " << Thing::count << std::endl; // Access using class name

    Thing t1;
    Thing t2;

    std::cout << "Count after creating t1 and t2: " << Thing::count << std::endl;

    Thing t3;

    std::cout << "Final count: " << t3.count << std::endl; // Can also access via an object

    return 0;
}
```

**Output:**

```
Initial count: 0
Thing 1 created.
Thing 2 created.
Count after creating t1 and t2: 2
Thing 3 created.
Final count: 3
Thing 3 destroyed.
Thing 2 destroyed.
Thing 1 destroyed.
```

## Static Member Functions

A static member function is a function that also belongs to the class rather than an object. It can be called directly using the class name and the scope resolution operator (`::`), without needing to create an object.

**Key Properties:**

- **No `this` Pointer:** A static function is not associated with any object, so it does not have a `this` pointer.
- **Access Limitations:** Because it has no `this` pointer, a static member function can **only** access other static members (both variables and functions) of the class. It cannot access non-static members.
- **Callable without an Object:** You can call it as `ClassName::functionName()`.

### Example

Static functions are often used as "helper" or "utility" functions that are related to the class but don't depend on the state of a specific object.

```cpp
#include <iostream>
#include <cmath>

class Vec3 {
public:
    double x, y, z;

    Vec3(double x=0, double y=0, double z=0) : x(x), y(y), z(z) {}

    // A regular member function - operates on 'this' object
    double magnitude() const {
        return std::sqrt(x*x + y*y + z*z);
    }

    // A static member function - does not operate on a specific object
    static Vec3 add(const Vec3& v1, const Vec3& v2) {
        // Cannot access non-static members here
        // return Vec3(x + v2.x, ...); // ERROR: 'x' is non-static

        return Vec3(v1.x + v2.x, v1.y + v2.y, v1.z + v2.z);
    }

    void print() const {
        std::cout << "(" << x << ", " << y << ", " << z << ")" << std::endl;
    }
};

int main() {
    Vec3 a(1, 2, 3);
    Vec3 b(4, 5, 6);

    // Call the static function using the class name
    Vec3 sum = Vec3::add(a, b);

    std::cout << "Vector a: "; a.print();
    std::cout << "Vector b: "; b.print();
    std::cout << "Sum: "; sum.print();

    // Call a regular member function on an object
    std::cout << "Magnitude of a: " << a.magnitude() << std::endl;

    return 0;
}
```

In this example, `Vec3::add` is a utility function that logically belongs to the `Vec3` class but doesn't need an instance to work. It takes two vectors and returns their sum. In contrast, `magnitude()` is a non-static function because it needs to know the `x`, `y`, and `z` values of a specific vector object.
