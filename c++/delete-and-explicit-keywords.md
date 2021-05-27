# delete
>The "delete" mechanism can be used for any function. For example, we can eliminate an undesired conversion like this:
>```cpp
>  struct Z {
>    // ...
>    Z(long long);     // can initialize with an long long   
>    Z(long) = delete; // but not anything less
>  };
>```
This modifier is useful for recourse-owning types such as `std::ifstream` and `std::unique_ptr` and `std::thread`. It usually used on copy construcotr and assignment assignment operator to make sure there's no concurrency issues on the associated recourses.


# explicit
>Prefixing the **explicit** keyword to the constructor prevents the compiler from using that constructor for implicit conversions.

Specifies that a constructor or conversion function (since C++11) or deduction guide (since C++17) is explicit, that is, it cannot be used for implicit conversions and copy-initialization.

Prefixing the `explicit` keyword to the constructor prevents the compiler from using that constructor for implicit conversions. Adding it to the above class will create a compiler error at the function call `DoBar (42)`. It is now necessary to call for conversion explicitly with  `DoBar (Foo (42))`.

Another example, suppose, you have a class `String`:
```cpp
class String {
public:
    String(int n); // allocate n bytes to the String object
    String(const char *p); // initializes object with char *p
};
```
Now, if you try:
```cpp
String mystring = 'x';
```
The character `'x'` will be implicitly converted to `int` and then the `String(int)` constructor will be called. But, this is not what the user might have intended. So, to prevent such conditions, we shall define the constructor as `explicit`:
```cpp
class String {
public:
    explicit String (int n); //allocate n bytes
    String(const char *p); // initialize sobject with string p
};
```


