# 26. Dynamic Memory Management

Dynamic memory management is the process of allocating and deallocating memory at runtime. Unlike stack memory, which is automatically managed, dynamic memory (allocated on the **heap**) must be explicitly managed by the programmer.

In C++, dynamic memory management is primarily handled using the `new` and `delete` operators.

## `new` Operator

The `new` operator is used to allocate memory on the heap. It allocates memory of the requested size and returns a pointer to the beginning of that block of memory.

### Allocating a Single Object

```cpp
// Allocate an integer on the heap
int* int_ptr = new int;
*int_ptr = 10;

// Allocate an object of a custom class
MyClass* obj_ptr = new MyClass();
```

### Allocating an Array

To allocate an array of objects, you use `new[]`.

```cpp
// Allocate an array of 50 integers
int* arr_ptr = new int[50];
arr_ptr[0] = 100; // Use it like a regular array
```

## `delete` Operator

The `delete` operator is used to deallocate memory that was previously allocated with `new`. It is crucial to deallocate memory when it is no longer needed to prevent memory leaks.

### Deallocating a Single Object

To deallocate a single object, you use `delete`.

```cpp
int* int_ptr = new int;
// ... use the pointer ...
delete int_ptr; // Frees the memory
int_ptr = nullptr; // Good practice to prevent using a dangling pointer
```

### Deallocating an Array

To deallocate an array, you **must** use `delete[]`. Using `delete` on an array results in undefined behavior (it usually only deallocates the first element and leaks the rest).

```cpp
int* arr_ptr = new int[50];
// ... use the array ...
delete[] arr_ptr; // Frees the entire array
arr_ptr = nullptr;
```

**Golden Rule:** For every `new`, there must be a corresponding `delete`. For every `new[]`, there must be a corresponding `delete[]`.

## Memory Leaks

A memory leak occurs when a program allocates memory on the heap but fails to deallocate it before the pointer to that memory is lost. The program loses the ability to access and free that memory, which remains allocated until the program terminates.

Memory leaks are serious bugs because they can cause a program to consume more and more memory over time, eventually leading to poor performance or a crash.

### Example of a Memory Leak

```cpp
void cause_leak() {
    // Allocate memory for an integer
    int* leak_ptr = new int(42);

    // The function ends, and the 'leak_ptr' variable is destroyed.
    // The memory it was pointing to was never freed with 'delete'.
    // This memory is now leaked.
}

int main() {
    for (int i = 0; i < 1000000; ++i) {
        cause_leak(); // This will leak a lot of memory!
    }
    return 0;
}
```

## Smart Pointers: The Modern Solution

Managing raw pointers with `new` and `delete` is error-prone. C++11 introduced **smart pointers** to automate memory management and prevent leaks. They are classes that wrap a raw pointer and manage its lifetime automatically using the principle of **RAII (Resource Acquisition Is Initialization)**. When the smart pointer object goes out of scope, its destructor is called, which automatically `delete`s the raw pointer.

You should **always prefer smart pointers over raw pointers** for owning dynamic memory.

### 1. `std::unique_ptr`

- Represents **exclusive ownership**.
- Only one `unique_ptr` can point to a given resource at any time.
- It is very lightweight (usually the same size as a raw pointer).
- You cannot copy a `unique_ptr`, but you can _move_ it to transfer ownership.

```cpp
#include <memory>

void use_unique_ptr() {
    std::unique_ptr<int> u_ptr(new int(100));
    // No need to call delete. Memory is automatically freed when u_ptr goes out of scope.
} // Memory for the int is freed here.
```

### 2. `std::shared_ptr`

- Represents **shared ownership**.
- Multiple `shared_ptr` instances can point to the same resource.
- It keeps a reference count of how many `shared_ptr`s are pointing to the resource.
- The resource is deallocated only when the last `shared_ptr` pointing to it is destroyed.

```cpp
#include <memory>
#include <vector>

void use_shared_ptr() {
    std::shared_ptr<int> s_ptr1 = std::make_shared<int>(200);
    {
        std::shared_ptr<int> s_ptr2 = s_ptr1; // Both pointers share the object. Reference count is 2.
        std::cout << "Ref count: " << s_ptr1.use_count() << std::endl;
    } // s_ptr2 is destroyed. Reference count is 1.
    std::cout << "Ref count: " << s_ptr1.use_count() << std::endl;
} // s_ptr1 is destroyed. Reference count becomes 0, and the memory is freed.
```

_Note: `std::make_shared` is the preferred way to create a `shared_ptr` as it is more efficient._

### 3. `std::weak_ptr`

- A non-owning "weak" reference to an object managed by a `shared_ptr`.
- It is used to break circular references between `shared_ptr` instances, which would otherwise cause a memory leak.
- It does not affect the reference count. You must convert it to a `shared_ptr` to access the object safely.
