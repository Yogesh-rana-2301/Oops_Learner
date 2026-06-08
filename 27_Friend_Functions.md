# 27. Friend Functions and Friend Classes

In C++, the `friend` keyword is used to grant a non-member function or another class special access to the `private` and `protected` members of a class. This selectively breaks encapsulation for specific, trusted entities.

While it seems to violate the principles of OOP, it is a pragmatic tool for situations where strict encapsulation is too restrictive or inefficient.

## Friend Functions

A friend function of a class is a non-member function that is given full access to the `private` and `protected` members of that class.

**Declaration:**
You declare a friend function by placing its prototype inside the class definition, prefixed with the `friend` keyword.

```cpp
class MyClass {
private:
    int private_data;
public:
    MyClass(int data) : private_data(data) {}

    // Declare a global function as a friend
    friend void showData(const MyClass& obj);
};

// Definition of the friend function (it's a normal, non-member function)
void showData(const MyClass& obj) {
    // Because it's a friend, it can access private members
    std::cout << "Private data: " << obj.private_data << std::endl;
}
```

### Use Cases for Friend Functions

1.  **Overloading Operators:** The most common use case. When overloading a binary operator (like `+` or `<<`) as a non-member function, you often need access to the private members of both operands.

    ```cpp
    #include <iostream>

    class Vector {
    private:
        double x, y;
    public:
        Vector(double x, double y) : x(x), y(y) {}

        // Declare the operator<< function as a friend
        friend std::ostream& operator<<(std::ostream& os, const Vector& v);
    };

    // The operator function needs to access v.x and v.y
    std::ostream& operator<<(std::ostream& os, const Vector& v) {
        os << "(" << v.x << ", " << v.y << ")";
        return os;
    }

    int main() {
        Vector v(1, 2);
        std::cout << v << std::endl; // Works because operator<< is a friend
        return 0;
    }
    ```

2.  **Factory Functions:** When a function is responsible for creating objects of a class but is not itself a member of that class, it might need friend access to initialize the object's private state.

## Friend Classes

You can also declare an entire class as a friend of another class. When `ClassB` is a friend of `ClassA`, all of `ClassB`'s member functions have unrestricted access to the `private` and `protected` members of `ClassA`.

**Declaration:**

```cpp
class ClassA {
private:
    int secret;
public:
    // Declare ClassB as a friend
    friend class ClassB;
};

class ClassB {
public:
    void revealSecret(ClassA& a) {
        // ClassB can access ClassA's private members
        a.secret = 42;
        std::cout << "ClassA's secret is: " << a.secret << std::endl;
    }
};
```

### Use Cases for Friend Classes

1.  **Tightly Coupled Classes:** When two or more classes are designed to work very closely together and form a single conceptual unit. For example, a `Node` class and a `LinkedList` class. The `LinkedList` needs intimate access to the `next` and `prev` pointers of its `Node`s, which should otherwise be private.

2.  **Testing:** A testing framework or a specific test class might be made a friend of the class under test to allow it to inspect the internal state of the object and verify its correctness without having to make all members public.

### Key Properties of Friendship

- **Not Mutual:** If `ClassB` is a friend of `ClassA`, it does not mean `ClassA` is a friend of `ClassB`.
- **Not Transitive:** If `ClassC` is a friend of `ClassB`, and `ClassB` is a friend of `ClassA`, it does not mean `ClassC` is a friend of `ClassA`.
- **Not Inherited:** Friendship is not inherited. A friend of a base class is not automatically a friend of its derived classes.

**Guideline:** Use `friend` sparingly. It is a useful tool for specific design problems, but overusing it can weaken encapsulation and make code harder to maintain. Always consider if the problem can be solved with the public interface first.
