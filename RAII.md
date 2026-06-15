# Smart Pointers in C++ (RAII)

## 1. `std::unique_ptr`

**Single ownership (cannot be copied)**

### Syntax

```cpp
#include <memory>

std::unique_ptr<int> p = std::make_unique<int>(10);
```

### Access

```cpp
*p = 20;       // dereference
```

For objects:

```cpp
class A {
public:
    void show() {}
};

std::unique_ptr<A> obj = std::make_unique<A>();
obj->show();
```

### Move ownership

```cpp
auto p2 = std::move(p);   // p becomes nullptr
```

---

## 2. `std::shared_ptr`

**Multiple ownership (reference counting)**

### Syntax

```cpp
#include <memory>

std::shared_ptr<int> p1 = std::make_shared<int>(10);
std::shared_ptr<int> p2 = p1;   // shared ownership
```

### Access

```cpp
*p1 = 50;
```

### Check reference count

```cpp
p1.use_count();   // number of owners
```

---

## 3. `std::weak_ptr`

**Non-owning reference (used with shared_ptr)**

### Syntax

```cpp
std::weak_ptr<int> w = p1;
```

### Safe access

```cpp
if (auto temp = w.lock()) {
    // temp is a shared_ptr
    *temp = 100;
}
```

---

## When to Use

| Situation        | Smart Pointer |
| ---------------- | ------------- |
| Single ownership | `unique_ptr`  |
| Shared ownership | `shared_ptr`  |
| Observing only   | `weak_ptr`    |

---

## Best Practices

* Prefer `unique_ptr` by default
* Use `shared_ptr` only when necessary
* Avoid raw `new` and `delete`
* Use `make_unique` and `make_shared`

---

## Key Takeaway

> RAII ensures automatic memory management using object lifetime.

* No memory leaks
* Exception safe
* Cleaner and safer code
