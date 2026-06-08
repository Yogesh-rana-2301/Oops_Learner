# 28. Inline Functions

An inline function is a function upon which the compiler has been requested to perform a specific optimization called **inlining**.

When a function is inlined, the compiler replaces the function call with the actual code of the function itself, right at the call site. This avoids the overhead associated with a normal function call.

## Function Call Overhead

A normal function call involves several steps that introduce a small amount of overhead:

1.  Pushing function arguments onto the stack.
2.  Pushing the return address onto the stack.
3.  Jumping to the function's memory address.
4.  Executing the function.
5.  Storing the return value.
6.  Popping arguments from the stack and jumping back to the return address.

For very small, frequently called functions, this overhead can be significant relative to the work the function actually does.

## The `inline` Keyword

The `inline` keyword is a **request** to the compiler to perform inlining. It is not a command. The compiler is free to ignore the request if it decides inlining is not appropriate.

**Syntax:**

```cpp
inline int add(int a, int b) {
    return a + b;
}
```

### How the Compiler Decides

The compiler uses heuristics to decide whether to inline a function. It is more likely to inline a function if:

- The function is very small (e.g., one or two lines of code).
- The function does not contain loops, `switch` statements, or recursion.
- The function is not virtual.

Conversely, the compiler will likely ignore the `inline` request for large or complex functions, because inlining them would bloat the executable code size, which can negatively impact performance (e.g., by causing more cache misses).

### Example

```cpp
#include <iostream>

// A good candidate for inlining
inline int square(int x) {
    return x * x;
}

int main() {
    int a = 5;
    int result = square(a); // Compiler may replace this with: int result = a * a;
    std::cout << "Result: " << result << std::endl;
    return 0;
}
```

Without inlining, the call to `square(a)` would involve the full function call overhead. With inlining, the code behaves as if you had written `int result = a * a;` directly in `main`, eliminating the overhead.

## Inline Functions in Header Files

One of the most important uses of the `inline` keyword relates to the **One Definition Rule (ODR)** in C++. The ODR states that a non-inline function can only have one definition across the entire program. If you define a function in a header file and include that header in multiple `.cpp` files, you will get a "multiple definition" linker error.

The `inline` keyword changes this rule. An inline function can be defined in multiple translation units (e.g., in a header file) as long as all definitions are identical.

This is why small functions defined inside a class declaration are implicitly `inline`.

```cpp
// In a header file (e.g., MyClass.h)
class MyClass {
public:
    // This function is implicitly inline because it's defined inside the class
    int getValue() const {
        return _value;
    }
private:
    int _value;
};
```

Because `getValue` is implicitly inline, you can include `MyClass.h` in many different `.cpp` files without causing a linker error. This is the primary modern use of the `inline` keyword: to allow function definitions in header files.

## Summary

- **Optimization:** `inline` is a request to the compiler to replace a function call with its body, avoiding function call overhead. It's best for small, simple functions.
- **Compiler's Choice:** The compiler can ignore the `inline` keyword. Modern compilers are very good at deciding when to inline on their own, even without the keyword.
- **One Definition Rule (ODR):** The main reason to use `inline` today is to allow you to place function definitions in header files, which is necessary for template functions and common for small member functions defined inside a class declaration.
