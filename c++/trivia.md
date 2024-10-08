### Inline Function
**Inline function** is a function that is expanded in line when it is called. When the inline function is called whole code of the inline function gets inserted or substituted at the point of inline function call. 


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
The C++ standard does not define the size of each type, but it does define the minimum width.

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
`typedef` is a compiler token: the preprocessor does not care about it. Also, `typedef` is equivalent to `using` for type alias.

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


### `()` and `{}` in constructors
`()` uses value initialization if the parentheses are empty, or direct initialization if non-empty.

`{}` uses list initialization, which implies value initialization if the braces are empty, or aggregate initialization if the initialized object is an aggregate.

### `forward<typename>()` and `move()`
- `std::move` is a **template function**, so it works on any type `T`; `std::forward` is a **template function**, so it works on any type `T`, and it takes its sole parameter via lvalue reference to a reference-less `T`; 

- `std::move` should be called without explicit template arguments and always results in an rvalue, while `std::forward` may end up as either. Use `std::move` when you know you no longer need the value and want to move it elsewhere, use `std::forward` to do that according to the values passed to your function template.

Here's an example:
```cpp
#include <iostream>
#include <memory>
#include <utility>
 
struct A {
    A(int&& n) { std::cout << "rvalue overload, n=" << n << "\n"; }
    A(int& n)  { std::cout << "lvalue overload, n=" << n << "\n"; }
};
 
class B {
public:
    template<class T1, class T2, class T3>
    B(T1&& t1, T2&& t2, T3&& t3) :
        a1_{std::forward<T1>(t1)},
        a2_{std::forward<T2>(t2)},
        a3_{std::forward<T3>(t3)}
    {
    }
 
private:
    A a1_, a2_, a3_;
};
 
//case 0
template<class T, class U>
std::unique_ptr<T> make_unique1(U&& u)
{
    return std::unique_ptr<T>(new T(std::forward<U>(u)));
}


//case 1
//template<class T, class U>
//std::unique_ptr<T> make_unique1(U&& u)
//{
//    return std::unique_ptr<T>(new T(u));
//}

//case 2
//template<class T, class U>
//std::unique_ptr<T> make_unique1(U&& u)
//{
//    return std::unique_ptr<T>(new T(std::move(u)));
//}
 
template<class T, class... U>
std::unique_ptr<T> make_unique2(U&&... u)
{
    return std::unique_ptr<T>(new T(std::forward<U>(u)...));
}
 
int main()
{
    auto p1 = make_unique1<A>(2); // rvalue
    int i = 1;
    auto p2 = make_unique1<A>(i); // lvalue
 
    std::cout << "B\n";
    auto t = make_unique2<B>(2, i, 3);
}
```
case 0 outputs:
>rvalue overload, n=2
>
>lvalue overload, n=1

case 1 outputs: 
>lvalue overload, n=2
>
>lvalue overload, n=1

case 2 outputs:
>rvalue overload, n=2
>
>rvalue overload, n=1

We can see here that by calling `move()`, two cases are casted to rvalues. In case 1, if dicretly call `new T(u)`, the constructor of `T` treat both cases as lvalues no matter what is supplied to `make_unique1`. By using `std::forward<typename>()`, the supplied parameters are passed as-it-is to the constructor of `T`.

### `const` in member functions
```cpp
const T& data() const { return data_; }
^^^^^
```
means it will return a const reference to T (here data_)
```cpp
Class c;
T& t = c.data()             // Not allowed.
const T& tc = c.data()      // OK.
```
```cpp
const T& data() const { return data_; }
                ^^^^^
```
means the function will not modify any member variables of the class (unless the member is mutable).
```cpp
void Class::data() const {
   this->data_ = ...;  // is not allowed here since data() is const (unless 'data_' is mutable)
   this->anything = ... // Not allowed unless the thing is 'mutable'
}
```
The benifits of using `const` are:
1. You tell the compiler that it will not modify anything in the object.
2. You can call the member function on constant object. If you don't have the `const` modifier then you can't call it on an object that has been defined as `const`.

### `static` members

- The static members of a class exist outside any object. Objects do not contain data associated with static data members.

- Similarly, static member functions are not bound to any object; they do not have a this pointer. As a result, static member functions may not be declared as const, and we may not refer to this in the body of a static member. This restriction applies both to explicit uses of this and to implicit uses of this by calling a nonstatic member.

- When we define a static member outside the class, we do not repeat the static keyword. The keyword appears only with the declaration inside the class body.
- We must define and initialize each static data member outside the class body.
- Like global objects, static data members are defined outside any function. Hence, once they are defined, they continue to exist until the program completes.
- The best way to ensure that the object is defined exactly once is to put the definition of static data members in the same file that contains the definitions of the class noninline member functions.
-  we can provide in-class initializers for static members that have const integral type and must do so for static members that are constexprs of literal type:
```cpp
class Account {
public:
    static double rate() { return interestRate; }
    static void rate(double);
private:
    static constexpr int period = 30;// period is a constant expression
    double daily_tbl[period];
};
```
- Even if a const static data member is initialized in the class body, that member ordinarily should be defined outside the class definition.

### `extern` keyword

It is useful when you share a variable between a few modules. You define it in one module, and use extern in the others.

To clarify, using `extern int x;` tells the compiler that an object of type int called `x` exists somewhere. It's not the compilers job to know where it exists, it just needs to know the type and name so it knows how to use it. Once all of the source files have been compiled, the **linker** will resolve all of the references of `x` to the one definition that it finds in one of the compiled source files. For it to work, the definition of the `x` variable needs to have what's called “external linkage”, which basically means that it needs to be declared outside of a function (at what's usually called “the file scope”) and without the static keyword.

For example:

in file1.cpp:
```cpp
int global_int = 1;
```
in file2.cpp:
```cpp
extern int global_int;
//in some function
cout << "global_int = " << global_int;
```

### lambda and its return

- use `[&]` to capture all variables in scope by reference, and use `[=]` to capture by value.
```cpp
[](int x) -> std::vector<int>{
             if (x%2)
                 return {1, 2};
             else
                 return {3, 4};
 });
 ```
The `-> std::vector<int>` operator, called trailing return type, tells the compiler that this lambda will return a vector of integers.

- The mutable specification allows a by-value parameter to be modified by the body of the lambda. A variable in the parameter list is typically captured const-by-value, so the mutable specification just removes this restriction.


### const_iterator and iterator

There is no performance difference.

A `const_iterator` is an iterator that points to const value (like a `const T*` pointer); dereferencing it returns a reference to a constant value (const T&) and prevents modification of the referenced value: it enforces const-correctness.

When you have a const reference to the container, you can only get a `const_iterator`.


### `static_cast` and (typename)

The classic C casts make no distinction between `static_cast<>()`, `reinterpret_cast<>()`, `const_cast<>()` and `dynamic_cast<>()`.

We shouldn't use `static_cast` for casting down an inheritance hierarchy, but rather `dynamic_cast`. That will return either the null pointer or a valid pointer.

### `#ifdef` macro

### Including the same header twice

- It has to work. Because you might include two headers in a .cpp file, each of which include a number of files, some of which might be included by both. Therefore, if duplicated including is not supported, soon all C++ code would fall apart.
- How does it work: Basically using `#ifndef HEADER_H`, `#define HEADER_H` and `#endif`. [example](https://stackoverflow.com/questions/3537260/why-its-valid-to-include-a-header-file-twice-in-c)
```cpp
#ifndef HEADER_H // HEADER_H is not defined, so we enter the "if" block
#define HEADER_H // HEADER_H is now defined

class MyClass {};// MyClass is now defined

#endif           // leaving the "if" block

#ifndef HEADER_H // HEADER_H *is* defined, so we do *not* enter the "if" block
//#define HEADER_H
//
//class MyClass {};
//
#endif           // end of the skipped "if" block
```

### I/O Manipulators

Comparing to `std::endl`, `\n` is always preferred unless a flush, which is included in `std::endl`, is required.

`std::cout<<std::boolalpha;` and other similar commands can change the output format.

