:white_check_mark: __kritsnasya__ 08/06/2026

# 18. Function Overriding

Function overriding is a feature of C++ that allows a derived class to provide a specific implementation for a member function that is already defined in its base class. This is a form of **runtime polymorphism** (or dynamic polymorphism).

When a derived class overrides a base class function, the version in the derived class is used for objects of the derived class. This allows you to have a common interface in the base class, but with specialized behavior in each subclass.

## Conditions for Function Overriding

For a function in a derived class to override a function in its base class, the following conditions must be met:

1.  The function must have the **exact same name**.
2.  The function must have the **exact same parameter list** (number, type, and order of parameters).
3.  The function must have the **same return type** (or a covariant return type).
4.  The function in the base class must be declared as `virtual`.

## Runtime Polymorphism

Function overriding is the core mechanism that enables runtime polymorphism. It allows you to treat objects of different derived classes as if they were objects of the base class, and yet have the correct, specific function called at runtime.

This is typically achieved by using a base class pointer or reference to refer to a derived class object.

### Example

Let's create a simple hierarchy where different animals make different sounds.

```cpp
#include <iostream>
#include <string>

class Animal {
public:
    // The 'virtual' keyword enables overriding and runtime polymorphism
    virtual void makeSound() const {
        std::cout << "Some generic animal sound" << std::endl;
    }
};

class Dog : public Animal {
public:
    // This function overrides the base class version
    void makeSound() const override { // 'override' is optional but highly recommended
        std::cout << "Woof!" << std::endl;
    }
};

class Cat : public Animal {
public:
    // This function also overrides the base class version
    void makeSound() const override {
        std::cout << "Meow!" << std::endl;
    }
};

// This function takes a reference to the base class
void playSound(const Animal& animal) {
    // The correct version of makeSound() is called at RUNTIME
    animal.makeSound();
}

int main() {
    Animal myAnimal;
    Dog myDog;
    Cat myCat;

    playSound(myAnimal); // Calls Animal::makeSound()
    playSound(myDog);    // Calls Dog::makeSound()
    playSound(myCat);    // Calls Cat::makeSound()

    return 0;
}
```

**Output:**

```
Some generic animal sound
Woof!
Meow!
```

In the `playSound` function, the parameter is `const Animal&`. Even though the compiler only knows it's an `Animal`, at runtime, the program determines the actual type of the object passed in (`Dog` or `Cat`) and calls the appropriate overridden `makeSound` function.

## The `override` Keyword (C++11 and later)

The `override` special identifier is used to indicate that a function in a derived class is intended to override a function in a base class.

While optional, it is **extremely good practice** to use it because it allows the compiler to help you. If you use `override` and the function does not correctly override a base class virtual function (e.g., due to a typo in the name or a different parameter list), the compiler will generate an error. This prevents subtle bugs.

### Example with `override` Error

```cpp
class Animal {
public:
    virtual void makeSound() const;
};

class Dog : public Animal {
public:
    // ERROR: Compiler will catch this mistake
    // 'makeSound' is misspelled as 'makeSoun'
    void makeSoun() const override {
        std::cout << "Woof!" << std::endl;
    }
};
```

Without `override`, this code would compile fine. You would have simply created a new, unrelated function named `makeSoun`, and calls through a base class pointer would silently call the `Animal` version instead of the `Dog` version you intended.

## Function Overloading vs. Function Overriding

| Feature                | Function Overloading                            | Function Overriding                                                |
| :--------------------- | :---------------------------------------------- | :----------------------------------------------------------------- |
| **Polymorphism**       | Compile-time (Static)                           | Runtime (Dynamic)                                                  |
| **Scope**              | Happens in the same class.                      | Happens between a base and a derived class.                        |
| **Function Signature** | Must have different signatures (parameters).    | Must have the same signature.                                      |
| **Keyword**            | None                                            | `virtual` in the base class, `override` in the derived class.      |
| **Resolution**         | The compiler resolves the call at compile time. | The call is resolved at runtime based on the object's actual type. |
