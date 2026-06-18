## 11. What is _cohesion_ in OOP?

**Cohesion** in OOP refers to how closely the methods and data within a single class are related to one another. A highly cohesive class is focused on a specific task or responsibility, making it easier to maintain, understand, and ensure reliability.

**High cohesion** is a desired attribute because it means that methods and properties within a class work together in a unified manner. In contrast, **low cohesion** indicates that a class has multiple, often unrelated responsibilities, making it harder to understand and maintain.

### Levels of Cohesion

1. **Coincidental**: Methods and properties within the class have no meaningful relationship.
2. **Logical**: Methods are grouped based on some logic but lack a clear theme.
3. **Temporal**: Methods are related by when they are executed, e.g., initialization methods.
4. **Procedural**: Methods are executed in a specific sequence.
5. **Communicational**: Methods work on the same set of data.
6. **Sequential**: The output of one method serves as the input for another.
7. **Functional**: All methods in the class contribute to a single well-defined task.

Of these, functional cohesion is the most desirable, as it closely aligns with the **Single Responsibility Principle**.

### Code Example: Low Cohesion Levels

Here is the Java code:

```java
public class FileUtility {

    public String readFile(String fileName) {
        // Read a file
        return content;
    }

    public void writeToDatabase(String data) {
        // Write content to a database
    }

    public void clearCache() {
        // Clear application cache
    }

    public List<String> parseFile(String content) {
        // Parse file content
        return parsedData;
    }
}
```

This `FileUtility` class exhibits low cohesion as it mixes file operations, database writing, and cache management.

### Recommendations for Improving Cohesion

1. **Single Responsibility Principle (SRP)**: Each class should have only one reason to change. This principle suggests that a class should focus on one task or responsibility.
2. **Encapsulation**: Encourage data hiding, and expose data only through focused and related methods.
<br>

## 12. What is _coupling_ in OOP?

**Coupling** in OOP describes the degree of interdependence between classes or modules. It determines how closely different modules or classes are linked to each other, impacting the system's flexibility, maintainability, and testability.

**Loose coupling** is generally preferred in software design.

### Types of Coupling

1. **Content Coupling**: This is the strongest form of coupling where one module directly accesses or modifies another module's internal data.

2. **Common Coupling**: Multiple modules share access to common global data. Any change to this shared resource can affect all the modules that depend on it.

3. **Control Coupling**: One module controls the flow of another by passing it control information, such as using flags.

4. **External Coupling**: Classes or modules are linked by external factors, such as configuration files or data schemas.

5. **Stamp (or Data) Coupling**: Modules share data structures and use only parts of them, requiring knowledge about the structure of the data being passed.

6. **Message Coupling**: The lowest form of coupling where modules communicate only through standard interfaces, such as method calls or messages.

### Relationship with SOLID Principles

- **Single Responsibility Principle (SRP)**: Adhering to SRP typically results in **low coupling** since classes have a singular focus, thereby minimizing dependencies.

- **Open-Closed Principle (OCP)**: Emphasizing extensibility without modification, OCP reduces the risk of **tight coupling** as extensions are typically made through interfaces or abstract classes.

- **Liskov Substitution Principle (LSP)**: When derived classes can replace their base classes without side effects, there's often a **reduction in coupling**, ensuring modules can operate independently of the specific derived class in use.

- **Interface Segregation Principle (ISP)**: By endorsing focused interfaces rather than "one-size-fits-all" ones, ISP naturally leads to **decreased coupling** as classes aren't forced to depend on methods they don't use.

- **Dependency Inversion Principle (DIP)**: By relying on abstractions rather than concrete implementations, DIP promotes **low coupling**, making systems more modular and adaptable.
<br>





## 15. Compare _inheritance_ vs. _mixin_ vs. _composition_.

**Inheritance**, **mixins**, and **composition** are all techniques in object-oriented programming that deal with code reuse and the relationship between objects or classes.

Let's look into the details and compare them:

### Inheritance

- **Definition**: A class (subclass) inherits properties and behaviors from another class (superclass).
- **Relationship**: "is-a" (e.g., a `Car` is a `Vehicle`).
- **Pros**: Direct way to reuse code; establishes intuitive relationships.
- **Cons**: Can lead to complex hierarchies; potential for over-generalization.

### Mixin

- **Definition**: A class that offers methods to other classes without being a standalone entity. Common in languages without multiple inheritance, such as Python.
- **Relationship**: "kind-of-a" or "can-do-this" (e.g., a `Helicopter` can fly like a `Bird`).
- **Pros**: Reuses code across classes; avoids deep inheritance issues.
- **Cons**: Method source can be unclear; potential name clashes.

### Composition

- **Definition**: Building objects by combining simpler ones. Emphasizes a "has-a" relationship.
- **Relationship**: "has-a" (e.g., a `Car` has an `Engine`).
- **Pros**: Encourages modular design; components can be easily swapped.
- **Cons**: May need more design upfront; can require more boilerplate code.
