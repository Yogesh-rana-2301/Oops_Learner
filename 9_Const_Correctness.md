# 9. Const Correctness

Const correctness is the practice of using the `const` keyword to prevent unintentional modification of data. It's a way of expressing your intent to the compiler and other programmers that a particular piece of data should not be changed.

Using `const` correctly helps the compiler catch errors at compile time, leads to safer and more readable code, and can even enable certain optimizations.

## `const` Functions (const Member Functions)

A `const` member function is a function that promises not to modify any of the member variables of the object it is called on.

To declare a member function as `const`, you place the `const` keyword after the parameter list.

**Syntax:**

```cpp
returnType functionName(parameters) const;
```

### Why Use `const` Member Functions?

1.  **Safety:** It prevents the function from accidentally changing the state of the object.
2.  **Clarity:** It clearly communicates to anyone using the class that this function is a "read-only" operation.
3.  **Allows Calling on `const` Objects:** This is the most important reason. <mark>You can only call `const` member functions on `const` objects.</mark>

### Example

```cpp
#include <iostream>
#include <string>

class Book {
private:
    string title;
    int pageCount;

public:
    Book(string t, int p) : title(t), pageCount(p) {}

    // A non-const member function (can modify the object)
    void setTitle(string newTitle) {
        title = newTitle;
    }

    // A const member function (promises not to modify the object)
    string getTitle() const {
        // ERROR: Cannot modify a member in a const function
        // pageCount++; // This would cause a compile error
        return title;
    }

    // Another const member function
    int getPageCount() const {
        return pageCount;
    }
};

void printBookTitle(const Book& book) {
    // 'book' is a const reference, so we can only call const member functions on it
    cout << book.getTitle() << endl;

    // ERROR: Cannot call a non-const function on a const object/reference
    // book.setTitle("New Title"); // This would cause a compile error
}

int main() {
    Book b1("The Lord of the Rings", 1178);

    // Creating a const object
    const Book b2("1984", 328);

    // Calling const functions on a non-const object is OK
    cout << b1.getTitle() << endl;

    // Calling const functions on a const object is OK
    cout << b2.getTitle() << endl;

    // Calling a non-const function on a const object is an ERROR
    // b2.setTitle("Animal Farm"); // This would cause a compile error

    printBookTitle(b1); // Pass a non-const object to the function
    printBookTitle(b2); // Pass a const object to the function

    return 0;
}
```

In the example, `getTitle()` and `getPageCount()` are marked `const` because they don't change the `Book` object. The `printBookTitle` function takes a `const Book&` to be efficient (avoids a copy) and safe (guarantees the function won't change the book). Because its parameter is `const`, it can only call `const` functions like `getTitle()`.

## `const` Objects

A `const` object is an object whose member variables cannot be changed after it is initialized by the constructor.

**Syntax:**

```cpp
const ClassName objectName(constructor_arguments);
```

As shown in the example above with `b2`, once a `const` object is created:

- Its member variables cannot be modified.
- You can **only** call `const` member functions on it.

This provides a strong guarantee of immutability, which is very useful for creating objects that represent fixed values or for passing objects to functions without fear of them being altered.

### A Note on `mutable`

There is an exception to the `const` rule. If you have a member variable that you want to be modifiable even inside a `const` member function, you can declare it with the `mutable` keyword. This is rare and should be used with caution. It's typically used for things like caching computed values or for synchronization primitives like mutexes that don't affect the "logical" state of the object.

```cpp
class DataProcessor {
private:
    mutable int cacheValue; // This can be changed in a const function
    bool cacheValid;

public:
    int getProcessedValue() const {
        if (!cacheValid) {
            // ... perform expensive calculation ...
            cacheValue = 42; // OK: cacheValue is mutable
            cacheValid = true; // ERROR: cacheValid is not mutable!
        }
        return cacheValue;
    }
};
```

_(Note: The above example has a logical flaw to illustrate the point. `cacheValid` would also need to be `mutable` for the caching logic to work correctly.)_
