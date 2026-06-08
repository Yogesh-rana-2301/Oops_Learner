:white_check_mark: __kritsnasya__ 08/06/2026

# 16. Function Overloading

Function overloading is a feature in C++ that allows you to define multiple functions with the same name in the same scope, as long as they have different parameter lists. This is a form of **compile-time polymorphism** (also known as static polymorphism) because the compiler determines which function to call at compile time based on the arguments provided.

The "signature" of a function, which includes the number, type, and order of its parameters, must be unique for each overloaded function. The return type alone is not sufficient to overload a function.

## How to Overload a Function

You can overload a function by changing:

1.  **The number of parameters.**
2.  **The type of parameters.**
3.  **The order of parameters.**

### Example

Let's create a set of overloaded `print` functions.

```cpp
#include <iostream>
#include <string>

// 1. A function that prints an integer
void print(int i) {
    std::cout << "Printing an integer: " << i << std::endl;
}

// 2. An overload that prints a double
void print(double d) {
    std::cout << "Printing a double: " << d << std::endl;
}

// 3. An overload that prints a string
void print(std::string s) {
    std::cout << "Printing a string: " << s << std::endl;
}

// 4. An overload with two parameters
void print(std::string s, int i) {
    std::cout << "Printing a string and an int: " << s << ", " << i << std::endl;
}

int main() {
    // The compiler chooses the correct function based on the arguments.
    print(10);          // Calls version 1
    print(3.14);        // Calls version 2
    print("Hello");     // Calls version 3
    print("Test", 123); // Calls version 4

    return 0;
}
```

**Output:**

```
Printing an integer: 10
Printing a double: 3.14
Printing a string: Hello
Printing a string and an int: Test, 123
```

## Why Use Function Overloading?

1.  **Code Readability and Simplicity:** It allows you to use the same function name for operations that are conceptually similar but apply to different data types. Instead of having `printInt()`, `printDouble()`, and `printString()`, you can just have `print()`. This makes the code cleaner and more intuitive.

2.  **Flexibility:** It provides multiple ways to perform a similar action, making your functions more versatile. For example, a constructor is often overloaded to provide different ways to initialize an object.

## Compile-Time Polymorphism

Function overloading is a form of <mark>compile-time polymorphism.</mark> Here's why:

- **Polymorphism** means "many forms." Here, the `print()` function has many forms.
- **Compile-Time** means the decision of which function to call is made when the code is compiled. The compiler looks at the function call (e.g., `print(10);`) and matches its argument list to the signature of one of the overloaded functions.

This is different from runtime polymorphism (achieved with virtual functions), where the decision is delayed until the program is actually running.

## Ambiguity in Overloading

Function overloading can sometimes lead to ambiguity if the compiler cannot determine the best match for a function call. This usually happens with automatic type conversions.

```cpp
void display(int i) {
    std::cout << "Integer: " << i << std::endl;
}

void display(double d) {
    std::cout << "Double: " << d << std::endl;
}

int main() {
    // display('a'); // AMBIGUITY ERROR!
    // A 'char' can be promoted to either 'int' or 'double'.
    // The compiler doesn't know which is a "better" conversion.
}
```

In such cases, the compiler will issue an error, and you must resolve the ambiguity, for example, by explicitly casting the argument: `display((int)'a');`.
