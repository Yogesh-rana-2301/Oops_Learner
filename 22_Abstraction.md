# 22. Abstraction

Abstraction is one of the four core principles of Object-Oriented Programming (OOP). It means focusing on the **essential qualities** of an object rather than the specific details of its implementation.

In simple terms, abstraction is about **hiding complexity and showing only what is necessary**.

Think about a real-world example: a car.

- **Abstraction (the interface):** To drive a car, you only need to know about the steering wheel, pedals, and gear stick. This is the essential interface for a driver.
- **Implementation (the hidden details):** You don't need to know how the engine works, how the fuel is injected, or how the transmission shifts gears. All this complexity is hidden from you.

In OOP, abstraction allows us to create a simplified, high-level view of a class, hiding its internal workings.

## How Abstraction is Achieved in C++

Abstraction is not a single feature but a design goal that is achieved using several C++ features, primarily:

1.  **Encapsulation (Data Hiding):** By using `private` and `protected` access specifiers, we hide the data and internal helper functions of a class. We only expose a `public` interface (the "steering wheel and pedals"). This is the most basic form of abstraction.

2.  **Abstract Classes and Pure Virtual Functions:** This is the most direct way to implement abstraction. An abstract class defines a pure **interface** without providing any implementation. It specifies _what_ a class should be able to do, but not _how_ it should do it.

## Interface Design

An **interface** is the set of public methods that a class provides for the outside world to interact with it. A well-designed interface is a key part of good abstraction.

**A good interface should be:**

- **Minimal:** It should only expose what is absolutely necessary.
- **Clear and Simple:** The function names should be intuitive and easy to understand.
- **Consistent:** It should follow consistent naming conventions and patterns.
- **Stable:** Once defined, the public interface should change as little as possible to avoid breaking code that uses it.

### Example: Abstraction through an Abstract Class

Let's design an interface for a data storage system. We don't care if the data is stored in a database, a file, or in memory. We just want a simple interface to save and load data.

```cpp
#include <iostream>
#include <string>
#include <fstream>

// The Abstract Base Class defines the INTERFACE
// It represents the concept of "something that can store data".
class IDataStorage {
public:
    virtual void saveData(const std::string& data) = 0;
    virtual std::string loadData() = 0;
    virtual ~IDataStorage() {} // Virtual destructor
};

// --- Implementation Details (Hidden from the user of the interface) ---

// Concrete class 1: Implements the interface by saving to a file
class FileStorage : public IDataStorage {
private:
    std::string filePath;
public:
    FileStorage(const std::string& path) : filePath(path) {}

    void saveData(const std::string& data) override {
        std::ofstream file(filePath);
        if (file.is_open()) {
            file << data;
            std::cout << "Data saved to file: " << filePath << std::endl;
        }
    }

    std::string loadData() override {
        std::ifstream file(filePath);
        std::string data;
        if (file.is_open()) {
            std::getline(file, data);
            std::cout << "Data loaded from file: " << filePath << std::endl;
        }
        return data;
    }
};

// --- High-level code that uses the abstraction ---

// This class doesn't know or care about files or databases.
// It only knows about the IDataStorage interface.
class Application {
private:
    IDataStorage& storage; // Works with ANY class that implements the interface
public:
    Application(IDataStorage& st) : storage(st) {}

    void run() {
        storage.saveData("This is a test.");
        std::string loaded = storage.loadData();
        std::cout << "Application loaded: " << loaded << std::endl;
    }
};

int main() {
    // The user can choose the implementation at runtime.
    FileStorage myFile("data.txt");

    // The Application is completely decoupled from the FileStorage implementation.
    Application app(myFile);
    app.run();

    return 0;
}
```

In this example:

- `IDataStorage` is the **abstraction**. It defines the simple, essential actions: `saveData` and `loadData`.
- `FileStorage` is a **concrete implementation**. It handles the complex details of opening, writing, and reading files.
- The `Application` class is completely **decoupled** from the implementation. It only interacts with the `IDataStorage` interface. You could easily create a `DatabaseStorage` class and pass it to the `Application` without changing a single line of code in `Application`.

This is the power of abstraction: it reduces complexity, increases flexibility, and allows for the creation of loosely coupled, maintainable systems.
