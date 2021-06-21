# Important General Rules

1. Smart pointers can not be casted to raw pointers. Instead, in very rare cases, the `get()` member function should be called to return a raw pointer.
2. Smart pointers can not be downcasted by using `static_cast` or `dynamic_cast`. Instead, `static_pointer_cast<>()` and `dynamic_pointer_cast<>()` should be used. Note that the typename enclosed in the angle brackets should not include `shared_ptr` keyword.
3. When statically downcasting a base pointer pointing to a base object to a derived pointer, the behaviour is unpredictable. When dynamically downcasting a base pointer in the same situation, the cast fails and returns a null pointer.

# `shared_ptr`

## Initialization 

1. Default
```cpp
shared_ptr<string> p1;    // shared_ptr that can point at a string
shared_ptr<list<int>> p2; // shared_ptr that can point at a list of ints
```
A default initialized smart pointer holds a null pointer.

2. `new`
```cpp
shared_ptr<double> p1; // shared_ptr that can point at a double
shared_ptr<int> p2(new int(42)); // p2 points to an int with value 42
```
This is actually a move copy constructor.

### make_shared
When we call `make_shared`, we must specify the type of object we want to create. Like the sequential-container `emplace` members, `make_shared` uses its arguments to construct an object of the given type. It returns a `shared_ptr` that points to the constructed object. Ordinarily we use `auto` to make it easier to define an object to hold the result of `make_shared`.
```cpp
// shared_ptr that points to an int with value 42
shared_ptr<int> p3 = make_shared<int>(42);
// p4 points to a string with value 9999999999
shared_ptr<string> p4 = make_shared<string>(10, '9');
// p5 points to an int that is value initialized (§ 3.3.1 (p. 98)) to 0
shared_ptr<int> p5 = make_shared<int>();
```

## Operations

- Common to `shared_ptr` and `unique_ptr`

![image](https://user-images.githubusercontent.com/78017591/121287947-21976c00-c915-11eb-9656-09fd286a75b2.jpeg)

- Particular to `shared_ptr`

![image](https://user-images.githubusercontent.com/78017591/121287987-3247e200-c915-11eb-8d3a-8162bba906e1.jpeg)

Basically they include the *copy assign operator* and *move assign operator* (make_shared) and copy constructor.

The smart pointer types define a function named `get` that returns a built-in pointer to the object that the smart pointer is managing. This function is intended for cases when we need to pass a built-in pointer to code that can’t use a smart pointer. The code that uses the return from get must not delete that pointer.

## Tracking the usage of a `shared_ptr`
>When we copy or assign a shared_ptr, each shared_ptr keeps track of how many other shared_ptrs point to the same object

We can think of a shared_ptr as if it has an associated counter, usually referred to as a **reference count**. 

The counter associated with a `shared_ptr` is incremented when:
1. Using it to initialize another `shared_ptr`
2. Using it as the right-hand operand of an assignment
3. Passing it to a function by value
4. Return it from a function by value

The counter is decremented when:
1. Assigning a new value to the `shared_ptr`
2. The `shared_ptr` itself is destroyed, such as when a local `shared_ptr` goes out of the scope.

Once a `shared_ptr`'s counter goes to zero, the `shared_ptr` automatically frees the object that it manages.
```cpp
auto r = make_shared<int>(42); // int to which r points has one user
r = q;  // assign to r, making it point to a different address
        // increase the use count for the object to which q points
        // reduce the use count of the object to which r had pointed
        // the object r had pointed to has no users; that object is automatically freed
```

## `shared_ptr` and exceptions

By default, when a shared_ptr is destroyed, it executes delete on the pointer it holds. To use a shared_ptr to manage a connection, we must first define a function to use in place of delete. It must be possible to call this deleter function with the pointer stored inside the shared_ptr. In this case, our deleter must take a single argument of type connection*:
```cpp
void end_connection(connection *p) { disconnect(*p); }
```
When we create a shared_ptr, we can pass an optional argument that points to a deleter function:
```cpp
void f(destination &d /* other parameters */)
{
    connection c = connect(&d);
    shared_ptr<connection> p(&c, end_connection);
    // use the connection
    // when f exits, even if by an exception, the connection will be properly closed
}
```

# `unique_ptr`

Unlike `shared_ptr`, there is no library function comparable to make_shared that returns a `unique_ptr`. Instead, when we define a unique_ptr, we **bind it to a pointer returned by `new`**. As with `shared_ptrs`, we must use the direct form of initialization:
```ccp
unique_ptr<double> p1;  // unique_ptr that can point at a double
unique_ptr<int> p2(new int(42)); // p2 points to int with value 42
```
**Edit**: From C++14, it's possible to call `make_unique` to initialize a unique_ptr.

```cpp
#include <iostream>
#include <iomanip>
#include <memory>
 
struct Vec3
{
    int x, y, z;
 
    // following constructor is no longer needed since C++20
    Vec3(int x = 0, int y = 0, int z = 0) noexcept : x(x), y(y), z(z) { }
 
    friend std::ostream& operator<<(std::ostream& os, const Vec3& v) {
        return os << "{ x=" << v.x << ", y=" << v.y << ", z=" << v.z << " }";
    }
};
 
int main()
{
    // Use the default constructor.
    std::unique_ptr<Vec3> v1 = std::make_unique<Vec3>();
    // Use the constructor that matches these arguments
    std::unique_ptr<Vec3> v2 = std::make_unique<Vec3>(0,1,2);
    // Create a unique_ptr to an array of 5 elements
    std::unique_ptr<Vec3[]> v3 = std::make_unique<Vec3[]>(5);
 
    std::cout << "make_unique<Vec3>():      " << *v1 << '\n'
              << "make_unique<Vec3>(0,1,2): " << *v2 << '\n'
              << "make_unique<Vec3[]>(5):   ";
    for (int i = 0; i < 5; i++) {
        std::cout << std::setw(i ? 30 : 0) << v3[i] << '\n';
    }
}
```

## deleter
```cpp
auto deleter=[&](int* ptr){...};
std::unique_ptr<int[], decltype(deleter)> ptr4(new int[4], deleter);
```

# `weak_ptr`
Binding a weak_ptr to a shared_ptr does not change the reference count of that shared_ptr. Once the last shared_ptr pointing to the object goes away, the object itself will be deleted. That object will be deleted even if there are weak_ptrs pointing to it.

## initialization
When we create a weak_ptr, we initialize it from a shared_ptr:
```cpp
auto p = make_shared<int>(42);
weak_ptr<int> wp(p);  // wp weakly shares with p; use count in p is unchanged
```
## Usage

Because the object might no longer exist, we cannot use a weak_ptr to access its object directly. To access that object, we must call lock. The lock function checks whether the object to which the weak_ptr points still exists. If so, lock returns a shared_ptr to the shared object.
```cpp
#include <iostream>
#include <memory>
 
std::weak_ptr<int> gw;
 
void observe()
{
    std::cout << "use_count == " << gw.use_count() << ": ";
    if (auto spt = gw.lock()) { // Has to be copied into a shared_ptr before usage
	std::cout << *spt << "\n";
    }
    else {
        std::cout << "gw is expired\n";
    }
}
 
int main()
{
    {
        auto sp = std::make_shared<int>(42);
	gw = sp;
 
	observe();
    }
 
    observe();
}
```
