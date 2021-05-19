### Inline Function
**Inline function** is a function that is expanded in line when it is called. When the inline function is called whole code of the inline function gets inserted or substituted at the point of inline function call. 

### make_shared
When we call `make_shared`, we must specify the type of object we want to create. Like the sequential-container `emplace` members, `make_shared` uses its argumentsto construct an object of the given type. It returns a `shared_ptr` that points to the constructed object. Ordinarily we use `auto` to make it easier to define an object to hold the result of `make_shared`.

### constexpr
The types we can use in a `constexpr` are known as "literal types" because they are simple enough to have literal values. The arithmetic, reference and pointer types are literal types. For example,
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

### assert
`assert` is a function-like macro using the syntax `assert(expr)`. It evaluates *expr* and if the expression is false (i.e., zero), then assert writes a message and terminates the program. if the expression is true (i.e., is nonzero), then `assert` does nothing.

### Documenting
```cpp
/**
 * Sum numbers in a vector.
 *
 * This sum is the arithmetic sum, not some other kind of sum that only
 * mathematicians have heard of.
 *
 * @param values Container whose values are summed.
 * @return sum of `values`, or 0.0 if `values` is empty.
 */
 ```
 Common tags include:
1. Short Summary
2. Extended Summary (recommended)
3. Template Parameters (if applicable; for classes, methods, and functions)
4. Function/Method Parameters (if applicable; for methods and functions)
5. Returns (if applicable; for methods and functions)
6. Throws (if applicable; for methods and functions)
7. Exception Safety (optional; for methods and functions)
8. Helper Functions (if applicable; for functions)
9. Initializer Declaration (optional; for constants)
10. See Also (optional)
11. Notes (optional)
12. References (optional)
13. Examples (optional)
