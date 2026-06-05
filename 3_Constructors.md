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

## Copy Constructor

A copy constructor is a special constructor that creates a new object as a copy of an existing object. It's used to initialize a new object from an existing one.

The syntax for a copy constructor is:

`ClassName(const ClassName &old_obj);`

- `const`: The `old_obj` is passed as a `const` reference because the copy constructor should not modify the original object.
- `&`: It's passed by reference to avoid making another copy, which would lead to an infinite recursive call to the copy constructor.

If you don't define a copy constructor, the compiler provides a default one. The default copy constructor performs a **shallow copy**, which means it copies the values of the member variables bit by bit.

### Shallow Copy vs. Deep Copy

- **Shallow Copy (Default):** The compiler-provided copy constructor does a shallow copy. If the object contains pointers, only the pointer addresses are copied, not the data they point to. Both the original and the copied object will point to the same memory location. This can lead to problems like double-freeing memory.

- **Deep Copy (User-Defined):** When you have pointers or dynamic memory allocation in your class, you should define your own copy constructor to perform a deep copy. A deep copy allocates new memory for the copied object and copies the actual data, so the original and the copy are independent.

### Example: User-Defined Copy Constructor (Deep Copy)

```cpp
#include <iostream>
#include <string>

class Wall {
private:
    double length;
    double height;
    int* style;

public:
    // Parameterized constructor
    Wall(double len, double h, int s) {
        length = len;
        height = h;
        style = new int(s);
        std::cout << "Parameterized constructor called." << std::endl;
    }

    // Copy constructor (Deep Copy)
    Wall(const Wall &other) {
        length = other.length;
        height = other.height;
        style = new int(*other.style); // Allocate new memory and copy the value
        std::cout << "Copy constructor called." << std::endl;
    }

    // Destructor to release memory
    ~Wall() {
        delete style;
        std::cout << "Destructor called." << std::endl;
    }

    void setStyle(int s) {
        *style = s;
    }

    void print() {
        std::cout << "Length: " << length << ", Height: " << height << ", Style: " << *style << std::endl;
    }
};

int main() {
    // Create an object
    Wall wall1(10.5, 2.5, 1);
    wall1.print();

    // Create a copy using the copy constructor
    Wall wall2 = wall1;
    wall2.print();

    // Modify the style of wall2
    // If it were a shallow copy, this would also change wall1's style
    wall2.setStyle(2);

    std::cout << "After modifying wall2:" << std::endl;
    wall1.print(); // Unchanged
    wall2.print(); // Changed

    return 0;
}
```

### When is a Copy Constructor Called?

1.  When an object is initialized from another object of the same class: `Wall wall2 = wall1;` or `Wall wall2(wall1);`
2.  When an object is passed by value to a function.
3.  When an object is returned by value from a function.

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
