# Summary of Pointers in C++

Pointers are variables that store the memory address of other variables. They are a fundamental concept in C++ and are used for dynamic memory allocation, creating data structures, and efficient function parameter passing.

### Declaring Pointers

A pointer is declared by using the `*` operator.

```cpp
int *ptr;    // Pointer to an integer
double *dPtr; // Pointer to a double
char *cPtr;   // Pointer to a character
```

### Initializing Pointers

A pointer can be initialized by assigning it the address of a variable using the `&` (address-of) operator.

```cpp
int var = 20;
int *ptr = &var; // ptr now holds the address of var
```

It's a good practice to initialize pointers to `nullptr` if they are not pointing to a valid memory address.

```cpp
int *ptr = nullptr;
```

### Dereferencing Pointers

The `*` operator is also used to dereference a pointer, which means accessing the value stored at the memory address the pointer is holding.

```cpp
int var = 20;
int *ptr = &var;

cout << *ptr; // Outputs 20
*ptr = 30;    // Changes the value of var to 30
```

### Pointers and Arrays

In C++, the name of an array is a constant pointer to the first element of the array.

```cpp
int arr[5] = {10, 20, 30, 40, 50};
int *ptr = arr; // ptr points to the first element of arr

cout << *ptr; // Outputs 10
cout << *(ptr + 1); // Outputs 20
```

### Pointers and Functions

Pointers can be passed to functions as arguments. This is known as "pass-by-pointer" or "pass-by-reference". It allows the function to modify the original variable.

```cpp
void increment(int *p) {
    (*p)++;
}

int main() {
    int a = 10;
    increment(&a);
    cout << a; // Outputs 11
    return 0;
}
```

### Dynamic Memory Allocation: `new` and `delete`

The `new` operator is used to allocate memory dynamically on the heap. The `delete` operator is used to deallocate the memory.

```cpp
int *ptr = new int; // Allocate memory for an integer
*ptr = 100;

delete ptr; // Deallocate the memory
ptr = nullptr; // Good practice to avoid dangling pointers

// Allocating an array
int *arr_ptr = new int[5];
delete[] arr_ptr; // Use delete[] for arrays
arr_ptr = nullptr;
```

### `nullptr`

`nullptr` is a keyword introduced in C++11 that represents a null pointer constant. It is type-safe and should be preferred over `NULL` or `0`.

```cpp
int *ptr = nullptr;
if (ptr == nullptr) {
    // ...
}
```
