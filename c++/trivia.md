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

### Data Types
As to the difference between the two sets, the C++ standard mandates minimum ranges for each, and that long long is at least as wide as long.
```
int型の数値の最小値: -2147483648
int型の数値の最大値: 2147483647
int型の数値のバイト数：４
long int型の数値の最小値: -9223372036854775808
long int型の数値の最大値: 9223372036854775807
long int型の数値のバイト数：８
long long int型の数値の最小値: -9223372036854775808
long long int型の数値の最大値: 9223372036854775807
long long　int型の数値のバイト数：８
```

### `#define` and `typedef`
`#define` is a preprocessor token: the compiler itself will never see it.
`typedef` is a compiler token: the preprocessor does not care about it.

`typedef` obeys scoping rules just like variables, whereas `define` stays valid until the end of the compilation unit (or until a matching undef).

Also, some things can be done with typedef that cannot be done with define. For example:
```cpp
typedef int* int_p1;
int_p1 a, b, c;  // a, b, c are all int pointers

#define int_p2 int*
int_p2 a, b, c;  // only the first is a pointer, because int_p2
                 // is replaced with int*, producing: int* a, b, c
                 // which should be read as: int *a, b, c
```

### callable objects
A callable object is something that can be called like a function, with the syntax `object()` or `object(args)`; that is, a function pointer, or an object of a class type that overloads `operator()`.

The overload of `operator()` in your class makes it callable.


### Const reference v.s. normal parameter
The difference is more prominent when you are passing a big struct/class.
```cpp
struct MyData {
    int a,b,c,d,e,f,g,h;
    long array[1234];
};
void DoWork(MyData md);
void DoWork(const MyData& md);
void DoWork(MyData const& md);
```
when you use use 'normal' parameter, you pass the parameter by value and hence creating a copy of the parameter you pass. if you are using const reference, you pass it by reference and the original data is not copied.

in both cases, the original data cannot be modified from inside the function.

### `bind()`
```cpp
void f(int n1, int n2, int n3, const int& n4, int n5)
{
    std::cout << n1 << ' ' << n2 << ' ' << n3 << ' ' << n4 << ' ' << n5 << '\n';
}
int n = 7;
auto f1 = std::bind(f, _2, 42, _1, std::cref(n), n);
f1(1, 2, 1001); // 1 is bound by _1, 2 is bound by _2, 1001 is unused
                // makes a call to f(2, 42, 1, n, 7)
```
>The arguments to bind are copied or moved, and are never passed by reference unless wrapped in `std::ref` or `std::cref`.
#### bind() v.s. lambda function
The following two are equavelent:
```cpp
struct foo
{
  template < typename A, typename B >
  void operator()(A a, B b)
  {
    cout << a << ' ' << b;
  }
};

auto f = bind(foo(), _1, _2);
f( "test", 1.2f ); // will print "test 1.2"
```
```cpp
auto f = []( auto a, auto b ){ cout << a << ' ' << b; }
f( "test", 1.2f ); // will print "test 1.2"
```
In C++14 there is nothing useful bind can do that can't also be done with lambdas.
