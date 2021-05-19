>Enumerations let uo group together sets of integral constants.

## Syntax
```cpp
enum class open_modes {input, output, append};  //scoped enumeration
enum color {red, yellow, green};  //unscoped enumeration
enum {dog, cat, fish} //unnamed, unscoped enum
```
## Specifying the Size of an `enum`
Although each `enum` defines a unique type, it is represented by one of the built-in integral types. We may specify that type by following the `enum` name with a colon and the name of the type we want to use.

The default type for scoped `enum`s is `int`, and there's no default for unscoped `enum`s.

## Forward Declarations
```cpp
enum intValues: unsigned long long;
enum class open_modes;
```
Because there's no default size for an unscoped enum, every declaration must include the size of that `enum`. We can declare a scoped `enum` without specifying a size, in which case the size is implicitly defined as `int`.

## Parameter Matching 
Although we can not pass an integral value to an `enum` parameter, we can pass an object or enumerator of an **unscoped** enumeration to a parameter of integral type.
