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


