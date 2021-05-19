## Inline Function
- **Inline function** is a function that is expanded in line when it is called. When the inline function is called whole code of the inline function gets inserted or substituted at the point of inline function call. 

## make_shared
- When we call `make_shared`, we must specify the type of object we want to create. Like the sequential-container `emplace` members, `make_shared` uses its argumentsto construct an object of the given type. It returns a `shared_ptr` that points to the constructed object. Ordinarily we use `auto` to make it easier to define an object to hold the result of `make_shared`.

## constexpr
- The types we can use in a `constexpr` are known as "literal types" because they are simple enough to have literal values. The arithmetic, reference and pointer types are literal types. For example,
```cpp
const     double PI1 = 3.141592653589793;
constexpr double PI2 = 3.141592653589793;
```
Both `PI1` and `PI2` are constant, meaning you can not modify them. However only `PI2` is a compile-time constant. It shall be initialized at compile time.  `PI1` **may be** initialized at compile time or run time. Furthermore, only `PI2` can be used in a context that requires a compile-time constant. For example:
```cpp
constexpr double PI3 = PI1;  // error
```
but:
```cpp
constexpr double PI3 = PI2;  // ok
```

## assert
- `assert` is a function-like macro using the syntax `assert(expr)`. It evaluates *expr* and if the expression is false (i.e., zero), then assert writes a message and terminates the program. if the expression is true (i.e., is nonzero), then `assert` does nothing.
