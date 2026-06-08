# 25. The Rule of Three / The Rule of Five

The "Rule of Three" and its modern successor, the "Rule of Five," are crucial guidelines in C++ for writing classes that manage resources correctly (e.g., dynamic memory, file handles, network sockets).

## The Rule of Three (Pre-C++11)

The Rule of Three states:

> If a class requires a user-defined **destructor**, a user-defined **copy constructor**, or a user-defined **copy assignment operator**, it almost certainly needs all three.

**The Logic:**
The need for a user-defined version of any of these three functions implies that the class is doing some form of resource management that the compiler-generated default functions can't handle (typically, managing dynamic memory via raw pointers).

- **Destructor:** You need a custom destructor to release the resource (e.g., `delete` memory).
- **Copy Constructor:** If you need a destructor, the default shallow-copy constructor is wrong. It would lead to two objects pointing to the same resource, resulting in a double-free when they are both destroyed. You need a deep-copy constructor.
- **Copy Assignment Operator:** Similarly, the default shallow-copy assignment operator is also wrong. It would leak the resource originally held by the left-hand object and then cause a double-free. You need a deep-copy assignment operator that handles self-assignment and resource cleanup.

### Example Implementing the Rule of Three

```cpp
#include <cstring> // For std::strcpy

class MyString {
private:
    char* _text;
    size_t _size;

public:
    MyString(const char* s = "") {
        _size = std::strlen(s);
        _text = new char[_size + 1];
        std::strcpy(_text, s);
    }

    // 1. Destructor
    ~MyString() {
        delete[] _text;
    }

    // 2. Copy Constructor
    MyString(const MyString& other) {
        _size = other._size;
        _text = new char[_size + 1];
        std::strcpy(_text, other._text);
    }

    // 3. Copy Assignment Operator
    MyString& operator=(const MyString& other) {
        if (this == &other) {
            return *this;
        }
        delete[] _text; // Free old resource
        _size = other._size;
        _text = new char[_size + 1]; // Allocate new resource
        std::strcpy(_text, other._text); // Copy data
        return *this;
    }
};
```

This class correctly manages its memory because it follows the Rule of Three.

## The Rule of Five (C++11 and later)

With the introduction of **move semantics** in C++11, the Rule of Three was extended to the Rule of Five. Move semantics provide a way to efficiently transfer resources from one object to another without expensive copying.

The Rule of Five states:

> If you define any of a destructor, copy constructor, copy assignment operator, **move constructor**, or **move assignment operator**, you should probably define or `=default` all five.

The two new members are:

- **Move Constructor:** `ClassName(ClassName&& other) noexcept;`
- **Move Assignment Operator:** `ClassName& operator=(ClassName&& other) noexcept;`

The `&&` denotes an "rvalue reference," which can bind to temporary objects (rvalues) that are about to be destroyed. Instead of copying the resource from a temporary object, we can "steal" its resource and leave the temporary in a valid but empty state. This is much more efficient.

### Example Implementing the Rule of Five

Let's add the move operations to our `MyString` class.

```cpp
#include <cstring>
#include <utility> // For std::swap

class MyString {
private:
    char* _text;
    size_t _size;

public:
    // ... Constructor, Destructor, Copy Constructor, Copy Assignment ...
    MyString(const char* s = "") { /* ... as before ... */ }
    ~MyString() { delete[] _text; }
    MyString(const MyString& other) { /* ... as before ... */ }
    MyString& operator=(const MyString& other) { /* ... as before ... */ }


    // 4. Move Constructor
    MyString(MyString&& other) noexcept
        : _text(other._text), _size(other._size) {
        // 'other' is a temporary object, so we can "steal" its pointer.
        // Leave the temporary object in a valid, destructible state.
        other._text = nullptr;
        other._size = 0;
    }

    // 5. Move Assignment Operator
    MyString& operator=(MyString&& other) noexcept {
        if (this == &other) {
            return *this;
        }
        delete[] _text; // Free our own resource

        // Steal the resources from 'other'
        _text = other._text;
        _size = other._size;

        // Leave 'other' in a valid state
        other._text = nullptr;
        other._size = 0;

        return *this;
    }
};
```

The `noexcept` keyword is important. It tells the compiler that this function will not throw an exception. This allows standard library containers (like `std::vector`) to use the more efficient move operations instead of falling back to copy operations when they need to reallocate their internal storage.

## The Rule of Zero

The best practice today is often called the **Rule of Zero**:

> Design your classes so that they don't need to manage any resources themselves.

Instead of using raw pointers, use standard library resource-managing classes (smart pointers, containers, etc.).

- `std::string` instead of `char*`
- `std::vector` instead of `T*` for a dynamic array
- `std::unique_ptr` or `std::shared_ptr` for a single dynamically allocated object

If you follow the Rule of Zero, the compiler-generated default versions of all five special member functions will work correctly, and you won't need to write any of them yourself. This is the safest and easiest approach.
