:white_check_mark: __kritsnasya__ 08/06/2026

# 17. Operator Overloading

Operator overloading is a form of compile-time polymorphism where you can provide a special meaning to a C++ operator for a user-defined data type (like a class or struct). It allows you to use operators like `+`, `-`, `==`, `<<`, etc., with objects of your own classes, making the code more intuitive and readable.

For example, instead of writing `Vector result = v1.add(v2);`, you can simply write `Vector result = v1 + v2;`, just as you would with built-in types like `int`.

## Basics and Intuition

The core idea is to make your custom types behave like built-in types. When you see `a + b`, you intuitively understand it as addition. Operator overloading lets you extend that intuition to your own objects.

You can overload most C++ operators, but you cannot:

- Overload operators for fundamental types (e.g., you can't change how `int + int` works).
- Create new operators (e.g., you can't create a `**` operator).
- Change the arity (number of operands), precedence, or associativity of an operator. For example, `*` will always have higher precedence than `+`.

### Syntax

To overload an operator, you define a special function named `operator@`, where `@` is the operator you want to overload. This can be done as a member function or a non-member (global/friend) function.

**As a member function:**

```cpp
class MyClass {
public:
    ReturnType operator@(const MyClass& rhs);
};
```

- The left-hand side of the operator is the `this` object.
- The right-hand side is passed as the argument.

**As a non-member function:**

```cpp
ReturnType operator@(const MyClass& lhs, const MyClass& rhs);
```

- The left-hand side is the first argument.
- The right-hand side is the second argument.

## Example: Overloading `+` for a `Vector` Class

Let's create a simple 2D vector class and overload the `+` operator to perform vector addition.

```cpp
#include <iostream>

class Vec2 {
public:
    double x, y;

    Vec2(double x = 0, double y = 0) : x(x), y(y) {}

    // Overload the '+' operator as a member function
    Vec2 operator+(const Vec2& rhs) const {
        // 'this->x' and 'this->y' are the left-hand side
        // 'rhs.x' and 'rhs.y' are the right-hand side
        return Vec2(this->x + rhs.x, this->y + rhs.y);
    }

    void print() const {
        std::cout << "(" << x << ", " << y << ")" << std::endl;
    }
};

int main() {
    Vec2 v1(1, 2);
    Vec2 v2(3, 4);
    Vec2 v3;

    // Use the overloaded '+' operator
    v3 = v1 + v2; // This is equivalent to v3 = v1.operator+(v2);

    std::cout << "v1: "; v1.print();
    std::cout << "v2: "; v2.print();
    std::cout << "v3 (v1 + v2): "; v3.print();

    return 0;
}
```

**Output:**

```
v1: (1, 2)
v2: (3, 4)
v3 (v1 + v2): (4, 6)
```

## Example: Overloading `<<` for Output Streams

A very common use case is overloading the stream insertion operator `<<` to allow easy printing of objects. This is best done as a **non-member function** because the left-hand operand is an `ostream` object (`std::cout`), not your class object.

```cpp
#include <iostream>

class Vec2 {
public:
    double x, y;
    Vec2(double x = 0, double y = 0) : x(x), y(y) {}
};

// Overload '<<' as a non-member function
// It takes the ostream by reference and returns it to allow chaining (e.g., cout << v1 << v2)
std::ostream& operator<<(std::ostream& os, const Vec2& vec) {
    os << "(" << vec.x << ", " << vec.y << ")";
    return os;
}

int main() {
    Vec2 v1(5, -3);
    Vec2 v2(1, 0);

    // Use the overloaded '<<' operator
    std::cout << "Vector v1 is " << v1 << " and v2 is " << v2 << std::endl;

    return 0;
}
```

**Output:**

```
Vector v1 is (5, -3) and v2 is (1, 0)
```

### When to Use Member vs. Non-Member Functions

- **Member Function:** Use for binary operators where the left operand is an object of the class and the operator modifies the object's state (e.g., `+=`, `-=`, `++`). Unary operators (`-`, `!`, `++`) are also often members.
- **Non-Member/Friend Function:** Use for binary operators where the left operand is not an object of the class (like `<<` where it's an `ostream`) or when you need type conversion on the left-hand operand. The `+` operator in our `Vec2` example could also have been a non-member function.

Operator overloading, when used judiciously, can significantly improve code clarity and align the behavior of custom types with the familiar semantics of built-in types.
