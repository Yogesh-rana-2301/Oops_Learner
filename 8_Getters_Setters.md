# 8. Getters and Setters

Getters and setters, also known as accessor and mutator methods, are public member functions that are used to retrieve (get) and update (set) the values of private member variables. They are a fundamental part of implementing encapsulation.

- **Getter (Accessor):** A method that reads the value of a private member variable. It provides read-only access to the object's state.
- **Setter (Mutator):** A method that modifies the value of a private member variable. It provides write access and can enforce rules about how the state can be changed.

## Controlled Access

By using getters and setters instead of making member variables public, a class can maintain control over its data.

### Example: Basic Getters and Setters

```cpp
#include <iostream>
#include <string>

class Student {
private:
    std::string name;
    int age;

public:
    // Setter for 'name'
    void setName(std::string newName) {
        name = newName;
    }

    // Getter for 'name'
    std::string getName() {
        return name;
    }

    // Setter for 'age'
    void setAge(int newAge) {
        age = newAge;
    }

    // Getter for 'age'
    int getAge() {
        return age;
    }
};

int main() {
    Student s1;

    // Use setters to modify the private data
    s1.setName("Alice");
    s1.setAge(20);

    // Use getters to retrieve the private data
    std::cout << "Name: " << s1.getName() << std::endl;
    std::cout << "Age: " << s1.getAge() << std::endl;

    return 0;
}
```

## Validation Logic

The most powerful feature of setters is their ability to contain validation logic. This ensures that the object's state remains valid and consistent. You cannot do this with public member variables.

### Example: Setter with Validation

Imagine a `Temperature` class that should only store values in Kelvin, which cannot be negative.

```cpp
#include <iostream>

class Temperature {
private:
    double kelvin;

public:
    // Default constructor
    Temperature() : kelvin(0.0) {}

    // Setter with validation logic
    void setKelvin(double k) {
        if (k >= 0) {
            kelvin = k;
        } else {
            // Handle the error: either do nothing, set a default, or throw an exception
            std::cout << "Error: Temperature cannot be negative." << std::endl;
        }
    }

    // Getter
    double getKelvin() {
        return kelvin;
    }

    // We can also provide getters that convert the value
    double getCelsius() {
        return kelvin - 273.15;
    }
};

int main() {
    Temperature t;

    t.setKelvin(300); // OK
    std::cout << "Temperature is " << t.getKelvin() << "K" << std::endl;
    std::cout << "Which is " << t.getCelsius() << "C" << std::endl;

    std::cout << "\nAttempting to set a negative temperature..." << std::endl;
    t.setKelvin(-10); // This will trigger the error message
    std::cout << "Temperature is now " << t.getKelvin() << "K" << std::endl; // Value remains 300

    return 0;
}
```

In this example, the `setKelvin` method acts as a gatekeeper. It prevents the `kelvin` member from being set to an invalid negative value, thus protecting the integrity of the object.

## Benefits of Getters and Setters

1.  **Data Protection:** They protect the object's data from being assigned invalid or inconsistent values.
2.  **Flexibility:** The internal representation of the data can change without affecting the code that uses the class. For example, you could change from Celsius to Fahrenheit internally, and as long as the `getCelsius()` method still returns the correct value, no external code needs to be modified.
3.  **Debugging and Logging:** You can easily add logging or debugging code inside a setter to track when and how a variable is being changed.
4.  **Computed Properties:** Getters don't have to return a raw variable; they can return a computed value (like the `getCelsius()` method above).
5.  **Read-Only or Write-Only Members:** You can provide only a getter to make a property read-only, or only a setter (less common) to make it write-only.

While adding getters and setters for every private member can be tedious, they provide a crucial layer of abstraction and control that is fundamental to robust object-oriented design.
