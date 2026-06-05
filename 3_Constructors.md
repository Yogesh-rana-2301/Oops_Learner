# 3. Constructors

A constructor is a special member function of a class that is automatically called when an object of that class is created. Its primary job is to initialize the object's member variables.

Key properties of a constructor:

- It has the same name as the class.
- It does not have a return type (not even `void`).
- It can be overloaded (you can have multiple constructors with different parameters).

## Default Constructor

A default constructor is a constructor that can be called with no arguments. This can be a constructor with no parameters, or one where all parameters have default values.

If you do not define any constructors in your class, the compiler will generate a public default constructor for you. This compiler-generated constructor will perform default initialization of members, which means fundamental types (like `int`, `double`, `bool`) will have indeterminate values, and class type members will have their own default constructors called.

### Example: Compiler-Generated Default Constructor

```cpp
#include <iostream>

class Player {
public:
    int score; // Will have an indeterminate value
};

int main() {
    Player p1;
    // p1.score is not guaranteed to be 0!
    std::cout << "Player score: " << p1.score << std::endl; // Could print anything
    return 0;
}
```

### Example: User-Defined Default Constructor

It's good practice to define your own default constructor to ensure members are initialized to sensible values.

```cpp
#include <iostream>

class Player {
public:
    int score;

    // User-defined default constructor
    Player() {
        score = 0;
        std::cout << "Default constructor called. Score initialized to 0." << std::endl;
    }
};

int main() {
    Player p1; // Calls the default constructor
    std::cout << "Player score: " << p1.score << std::endl; // Prints 0
    return 0;
}
```

## Parameterized Constructor

A parameterized constructor is a constructor that accepts one or more arguments. This allows you to create an object and initialize its member variables with specific values at the time of creation.

### Example

```cpp
#include <iostream>
#include <string>

class Book {
public:
    std::string title;
    std::string author;
    int pages;

    // Parameterized constructor
    Book(std::string t, std::string a, int p) {
        title = t;
        author = a;
        pages = p;
        std::cout << "Parameterized constructor called for: " << title << std::endl;
    }
};

int main() {
    // Create an object using the parameterized constructor
    Book myBook("The Hitchhiker's Guide to the Galaxy", "Douglas Adams", 224);

    std::cout << "Author: " << myBook.author << std::endl;
    std::cout << "Pages: " << myBook.pages << std::endl;

    return 0;
}
```

### Using Initializer Lists

A more efficient and preferred way to initialize members in a constructor is by using a member initializer list. This initializes members directly, rather than assigning to them inside the constructor body. For `const` members and reference members, you _must_ use an initializer list.

```cpp
class Book {
public:
    std::string title;
    std::string author;
    const int pages; // const member

    // Parameterized constructor with initializer list
    Book(std::string t, std::string a, int p)
        : title(t), author(a), pages(p) // Initializer list
    {
        std::cout << "Initializer list constructor called for: " << title << std::endl;
    }
};
```

## Constructor Overloading

Constructor overloading is the practice of defining multiple constructors in the same class, each with a different set of parameters (different number of parameters or different types of parameters). This provides flexibility in how objects are created.

### Example

```cpp
#include <iostream>
#include <string>

class Rectangle {
public:
    int width;
    int height;

    // 1. Default constructor
    Rectangle() : width(1), height(1) {
        std::cout << "Default constructor (1x1)" << std::endl;
    }

    // 2. Parameterized constructor for a square
    Rectangle(int side) : width(side), height(side) {
        std::cout << "Square constructor (" << side << "x" << side << ")" << std::endl;
    }

    // 3. Parameterized constructor for a rectangle
    Rectangle(int w, int h) : width(w), height(h) {
        std::cout << "Rectangle constructor (" << w << "x" << h << ")" << std::endl;
    }
};

int main() {
    Rectangle r1;         // Calls constructor 1
    Rectangle r2(5);      // Calls constructor 2
    Rectangle r3(4, 6);   // Calls constructor 3

    return 0;
}
```
