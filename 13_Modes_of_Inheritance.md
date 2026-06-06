# 13. Modes of Inheritance

In C++, when a class inherits from a base class, you can specify an **inheritance mode**. This mode determines the access level of the inherited members from the base class in the derived class. It acts as a filter, potentially restricting the access of members that were more accessible in the base class.

There are three modes of inheritance: `public`, `protected`, and `private`.

## `public` Inheritance

This is the most common mode of inheritance. It represents a pure "is-a" relationship. The access levels of the inherited members do not change.

- `public` members of the base class remain `public` in the derived class.
- `protected` members of the base class remain `protected` in the derived class.
- `private` members of the base class are **never accessible** in the derived class, regardless of the mode.

**Rule:** `public` -> `public`, `protected` -> `protected`.

### Example

```cpp
class Base {
public:
    int publicMember;
protected:
    int protectedMember;
private:
    int privateMember;
};

class Derived : public Base {
    // publicMember is public here
    // protectedMember is protected here
    // privateMember is not accessible here

public:
    void accessMembers() {
        publicMember = 1;    // OK
        protectedMember = 2; // OK
        // privateMember = 3;   // ERROR
    }
};

int main() {
    Derived d;
    d.publicMember = 10; // OK, because it's public in Derived
    // d.protectedMember = 20; // ERROR, protected members are not accessible from outside
    return 0;
}
```

## `protected` Inheritance

In `protected` inheritance, all `public` and `protected` members of the base class become `protected` in the derived class.

- `public` members of the base class become `protected` in the derived class.
- `protected` members of the base class remain `protected` in the derived class.

**Rule:** `public` -> `protected`, `protected` -> `protected`.

This mode is less common. It's used when you want the derived class and its subsequent subclasses to have access to the base class's public interface, but you want to hide that interface from the outside world.

### Example

```cpp
class Base {
public:
    int publicMember;
protected:
    int protectedMember;
};

class Derived : protected Base {
    // publicMember is protected here
    // protectedMember is protected here

public:
    void accessMembers() {
        publicMember = 1;    // OK
        protectedMember = 2; // OK
    }
};

int main() {
    Derived d;
    // d.publicMember = 10; // ERROR, publicMember is now protected in Derived
    return 0;
}
```

## `private` Inheritance

In `private` inheritance, all `public` and `protected` members of the base class become `private` in the derived class. This means they are only accessible within the derived class and cannot be accessed by any further subclasses.

- `public` members of the base class become `private` in the derived class.
- `protected` members of the base class become `private` in the derived class.

**Rule:** `public` -> `private`, `protected` -> `private`.

Private inheritance models a **"is-implemented-in-terms-of"** relationship, not an "is-a" relationship. You are inheriting the implementation details, but not the public interface.

### Example

```cpp
class Base {
public:
    int publicMember;
protected:
    int protectedMember;
};

class Derived : private Base {
    // publicMember is private here
    // protectedMember is private here

public:
    void accessMembers() {
        publicMember = 1;    // OK
        protectedMember = 2; // OK
    }
};

class GrandChild : public Derived {
public:
    void accessGrandParent() {
        // publicMember = 10; // ERROR: publicMember is private in Derived
    }
};

int main() {
    Derived d;
    // d.publicMember = 10; // ERROR, publicMember is now private in Derived
    return 0;
}
```

## Summary Table

This table shows what happens to `Base` class members when inherited by a `Derived` class using different modes.

| Base Class Access | `public` Inheritance | `protected` Inheritance | `private` Inheritance |
| :---------------- | :------------------- | :---------------------- | :-------------------- |
| **`public`**      | `public`             | `protected`             | `private`             |
| **`protected`**   | `protected`          | `protected`             | `private`             |
| **`private`**     | _Inaccessible_       | _Inaccessible_          | _Inaccessible_        |

**Default Mode:** If you omit the inheritance mode (e.g., `class Derived : Base`), the default mode is `private` for classes and `public` for structs. It's always better to be explicit.
