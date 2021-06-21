>A *templates* is a blueprint or formula for creating classes or functions.

# Preliminaries
- The compiler will instantiate different versions of a template function. Those compiler-generated functions are generally refered to as an **instantiation** of the template.
- `initializer_list` can define a function that can take a variying number of arguments **with the same type**. `variadic template` can define a similar function with different types.

## Syntax
```cpp
template<typename ... Args> someFunction(Args ... args)
```
The number of parameters in the function parameter pack can be 0 or more.

## Forwarding Parameter Packs
A common pattern:
```cpp
template<typename ... Args>
void fun(Args&& ... args) {
  work(std::forward<Args>(args)...);
}
```

Note that `std::forward<typename>()` is not only applicable for parameter packs. It can also cast a templated function parameter as-it-is to the inner functions.

## An example

**The recursion has to be terminated when there is only one type parameter left.**

```cpp
// function to end the recursion and print the last element
// this function must be declared before the variadic version of print is defined
template<typename T>
ostream &print(ostream &os, const T &t)
{
    return os << t; // no separator after the last element in the pack
}
// this version of print will be called for all but the last element in the pack
template <typename T, typename... Args>
ostream &print(ostream &os, const T &t, const Args&... rest)
{
    os << t << ", ";           // print the first argument
    return print(os, rest...); // recursive call; print the other arguments
}
```
# Variadic Class Template

```cpp
template <typename ... T>
struct Group;
```

The usual implementation of such templates is again recursive, using deeply nested inheritance. This is done using a partial specialization, so we will leave the general template we showed previously as a declaration only, and define the specialization for one type parameter:

```cpp
template <typename T1>
struct Group<T1> {
    T1 t1_;
    Group() = default;
    explicit Group(const T1& t1) : t1_(t1) {}
    explicit Group(T1&& t1) : t1_(std::move(t1)) {}
    explicit operator const T1&() const { return t1_; }
    explicit operator T1&() { return t1_; }
};
```

Then the variadic template is implemented. There are two points to note in this example:
1. The `Group<T1, T ...>` must be inherited from `Group<T ...>`, otherwise the `Group<T ...>` is not a declared class and cannot be used.
2. Since T1 and T are typenames, `T1&&` and `T&&` are not rvalue references but forwarding references which can be bound to both lvalues and rvalues.

```cpp
template <typename T1, typename ... T>
struct Group<T1, T ...> : Group<T ...> {
    T1 t1_;
    Group() = default;
    explicit Group(const T1& t1, T&& ... t) :
        Group<T ...>(std::forward<T>(t) ...), t1_(t1) {}
    explicit Group(T1&& t1, T&& ... t) :
        Group<T ...>(std::forward<T>(t) ...), t1_(std::move(t1)) {}
    explicit operator const T1&() const { return t1_; }
    explicit operator T1&() { return t1_; }
};
```

