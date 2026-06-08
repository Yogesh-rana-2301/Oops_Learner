# 7. Access Specifiers

Access specifiers (or access modifiers) in C++ are keywords that define the accessibility of class members (both attributes and methods). They are a core part of implementing encapsulation and data hiding.

There are three access specifiers: `public`, `private`, and `protected`.

## `public`

Members declared as `public` are accessible from anywhere in the program. They can be accessed by member functions of the same class, member functions of derived classes, and also by any non-member function (like `main()`).

The `public` members form the "interface" of the class, which is how external code interacts with an object.

### Example

```cpp
class Circle {
public:
    double radius; // Public attribute

    double getArea() { // Public method
        return 3.14159 * radius * radius;
    }
};

int main() {
    Circle c;
    c.radius = 5.0; // OK: radius is public
    double area = c.getArea(); // OK: getArea() is public
    return 0;
}
```

## `private`

Members declared as `private` are only accessible from within the class itself. They cannot be accessed by derived classes or by any non-member function. This is the highest level of data protection.

By default, all members of a `class` are `private` if no access specifier is provided.

### Example

```cpp
class BankAccount {
private:
    double balance; // Private attribute

    bool hasSufficientFunds(double amount) { // Private helper method
        return balance >= amount;
    }

public:
    BankAccount(double initialBalance) : balance(initialBalance) {}

    void withdraw(double amount) {
        if (hasSufficientFunds(amount)) { // OK: Member function can access private member
            balance -= amount;
        }
    }
};

int main() {
    BankAccount acc(1000);
    // ERROR: 'balance' is private
    // acc.balance = 5000;

    // ERROR: 'hasSufficientFunds' is private
    // if (acc.hasSufficientFunds(500)) { ... }

    acc.withdraw(500); // OK: withdraw() is public
    return 0;
}
```

## `protected`

Members declared as `protected` are a middle ground between `public` and `private`. They are:

1.  Accessible from within the class itself (just like `private`).
2.  Accessible from within any **derived classes** (subclasses).
3.  **Not** accessible by non-member functions (like `main()`).

`protected` is used when you want to hide implementation details from the outside world but still allow those details to be accessible and modifiable by classes that inherit from the base class.

### Example

```cpp
#include <iostream>
#include <string>

class Person {
protected:
    string name; // Protected attribute

public:
    Person(string n) : name(n) {}
};

class Student : public Person {
public:
    // The Student constructor needs to initialize the Person part of the object
    Student(string n) : Person(n) {}

    void introduce() {
        // OK: Derived class can access protected member of the base class
        cout << "Hi, my name is " << name << " and I am a student." << endl;
    }
};

int main() {
    Student s("Alice");
    s.introduce();

    // ERROR: 'name' is protected and not accessible from main()
    // s.name = "Bob";

    return 0;
}
```

## Deep Difference Summary

| Specifier       | Accessible from Same Class | Accessible from Derived Class | Accessible from Outside (World) | Primary Use Case                                                                                                                                                       |
| :-------------- | :------------------------- | :---------------------------- | :------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`public`**    | Yes                        | Yes                           | Yes                             | Creating the public interface of the class.                                                                                                                            |
| **`protected`** | Yes                        | Yes                           | No                              | Allowing derived classes to access and modify base class implementation details that are hidden from the rest of the world. Essential for designing class hierarchies. |
| **`private`**   | Yes                        | No                            | No                              | Hiding the internal state and implementation details of a class completely. The default for secure, encapsulated design.                                               |

### `struct` vs `class`

In C++, `struct` and `class` are almost identical. The only difference is their default access specifier:

- In a `class`, members are `private` by default.
- In a `struct`, members are `public` by default.

You can use any of the access specifiers in both `struct`s and `class`es. By convention, `struct`s are used for simple data aggregates where all members are public, while `class`es are used for objects that have complex behavior and require encapsulation.
