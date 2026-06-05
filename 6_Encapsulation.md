# 6. Encapsulation

Encapsulation is one of the four fundamental principles of Object-Oriented Programming (OOP), along with inheritance, polymorphism, and abstraction. It refers to the bundling of data (attributes) and the methods (functions) that operate on that data into a single unit, known as a class.

The primary goal of encapsulation is to hide the internal state of an object from the outside world and only expose a controlled, public interface.

## Data Hiding

Data hiding is the mechanism of restricting access to certain components of an object. It is the key concept that makes encapsulation possible. In C++, data hiding is achieved by using access specifiers (`private`, `protected`).

- **Data Members (Attributes):** These are typically declared as `private`. This means they cannot be accessed directly from outside the class.
- **Member Functions (Methods):** A set of `public` methods are provided to manipulate the private data. These methods are often called "getters" and "setters" or an "accessor" and "mutator".

By making the data private and the methods public, the class maintains complete control over its internal state.

### Example

```cpp
#include <iostream>
#include <string>

class Employee {
private:
    // Private data members - hidden from the outside
    std::string name;
    double salary;

public:
    // Public constructor to initialize the object
    Employee(std::string n, double s) {
        name = n;
        salary = s;
    }

    // Public "setter" method to modify the salary
    void setSalary(double newSalary) {
        if (newSalary > 0) { // The class controls how data is changed
            salary = newSalary;
        }
    }

    // Public "getter" method to access the salary
    double getSalary() {
        return salary;
    }

    // Public method to display info
    void display() {
        std::cout << "Name: " << name << ", Salary: " << salary << std::endl;
    }
};

int main() {
    Employee emp("John Doe", 50000);

    // ERROR: Cannot access private member directly
    // emp.salary = -1000; // This line would cause a compile error

    // CORRECT: Use the public method to modify the data
    emp.setSalary(55000);
    emp.display();

    // Use the public method to access the data
    std::cout << "John's salary is: " << emp.getSalary() << std::endl;

    return 0;
}
```

In this example, the `salary` is `private`. You cannot just set it to any value from `main()`. You must go through the `setSalary` method, which can enforce rules (like ensuring the salary is positive).

## Why Encapsulation Matters

Encapsulation provides several key benefits in software design:

1.  **Control and Security:** It gives you full control over the data. You can enforce validation rules and logic within the setter methods to ensure the object's state remains valid and consistent. This prevents accidental or malicious corruption of data.

2.  **Flexibility and Maintainability:** It decouples the internal implementation of a class from the code that uses it. You can change how the class stores its data or the logic within its methods without breaking any of the external code, as long as the public interface (the method signatures) remains the same. For example, you could change the `salary` from a `double` to an `int` and only need to update the class internals; the code calling `getSalary()` might not need to change at all.

3.  **Simplicity:** It simplifies the use of a class. The user of the class doesn't need to know about its complex internal workings. They only need to interact with the simple, well-defined public interface. This reduces the cognitive load on the programmer using the class.

4.  **Reduced Complexity:** By hiding implementation details, encapsulation helps manage the complexity of large software systems. It allows you to think about the system at a higher level of abstraction, focusing on the interactions between objects rather than the nitty-gritty details of each one.
