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
