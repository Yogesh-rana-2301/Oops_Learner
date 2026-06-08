# 24. Assignment Operator (`operator=`)

The assignment operator (`operator=`) is a special operator used to copy the values from one existing object to another existing object. It is fundamentally different from the copy constructor.

- **Copy Constructor:** Initializes a _new_ object from an existing one.
- **Assignment Operator:** Copies data into an _already existing_ object.

```cpp
Widget w1;
Widget w2 = w1; // Initialization -> Calls Copy Constructor

Widget w3;
w3 = w1;        // Assignment -> Calls Assignment Operator
```

Like the copy constructor, the compiler provides a default assignment operator if you don't define one. This default version performs a member-wise shallow copy, which leads to the same problems (dangling pointers, double frees) if your class manages dynamic memory.

## Overloading the Assignment Operator

To ensure correct behavior for classes that manage resources, you must overload the assignment operator to perform a deep copy.

**Syntax:**
`ClassName& operator=(const ClassName& rhs);`

An overloaded assignment operator should have these key features:

1.  **Parameter:** It takes a `const` reference to the right-hand side (`rhs`) object.
2.  **Return Value:** It returns a reference to the left-hand side object (`*this`) to allow for chained assignments (e.g., `a = b = c;`).
3.  **Self-Assignment Check:** It must check if an object is being assigned to itself (e.g., `w1 = w1;`). This is crucial to prevent self-destruction.
4.  **Resource Management:** It must properly clean up any resources the left-hand side object is already holding before allocating new ones.

### Example: Overloading `operator=` for Deep Copy

Let's extend our `DeepBox` class from the previous topic.

```cpp
#include <iostream>

class DeepBox {
public:
    int* data;

    DeepBox(int val) {
        data = new int(val);
    }

    ~DeepBox() {
        delete data;
    }

    // Copy Constructor (for completeness)
    DeepBox(const DeepBox& other) {
        data = new int(*other.data);
    }

    // Overloaded Assignment Operator
    DeepBox& operator=(const DeepBox& rhs) {
        std::cout << "Assignment operator called." << std::endl;

        // 1. Self-assignment check
        if (this == &rhs) {
            return *this;
        }

        // 2. Clean up existing resource in the left-hand side object
        delete data;

        // 3. Allocate new memory and copy the data (Deep Copy)
        data = new int(*rhs.data);

        // 4. Return a reference to the current object
        return *this;
    }
};

int main() {
    DeepBox box1(100);
    DeepBox box2(200);

    std::cout << "Before assignment: " << *box1.data << ", " << *box2.data << std::endl;

    box1 = box2; // Assignment operator is called

    std::cout << "After assignment: " << *box1.data << ", " << *box2.data << std::endl;

    // Now, let's change box2's data to prove they are independent
    *box2.data = 300;

    std::cout << "After modification: " << *box1.data << ", " << *box2.data << std::endl;

    // Test self-assignment
    box1 = box1;
    std::cout << "After self-assignment: " << *box1.data << std::endl;

    return 0;
}
```

**Output:**

```
Before assignment: 100, 200
Assignment operator called.
After assignment: 200, 200
After modification: 200, 300
Assignment operator called.
After self-assignment: 200
```

### Breakdown of the `operator=` Implementation

1.  `if (this == &rhs)`
    - `this` is a pointer to the left-hand side object.
    - `&rhs` is the address of the right-hand side object.
    - If they are the same, we are doing `x = x`. If we didn't check for this, we would `delete data` and then try to copy from `*rhs.data`, which would now be a dangling pointer. We simply return `*this` to do nothing.

2.  `delete data;`
    - The `box1` object already holds a piece of memory (containing the value 100). Before we assign the new data from `box2`, we must release this old memory to prevent a memory leak.

3.  `data = new int(*rhs.data);`
    - This is the deep copy. We allocate new memory for `box1` and copy the _value_ from `box2`'s data into it.

4.  `return *this;`
    - This allows assignment chaining. In an expression like `a = b = c`, the `b = c` part is evaluated first. The `operator=` for `b` returns a reference to `b`, which then becomes the right-hand side for the `a = b` assignment.

Properly overloading the assignment operator is essential for creating robust classes that manage their own resources, forming a key part of the **Rule of Three/Five**.
