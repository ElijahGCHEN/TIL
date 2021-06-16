A function pointer is just that—a pointer that denotes a function rather than an object. Like any other pointer, a function pointer points to a particular type. A function’s type is determined by its return type and the types of its parameters. The function’s name is not part of its type. For example:
```cpp
// compares lengths of two strings
bool lengthCompare(const string &, const string &);
```
has type `bool(const string&, const string&)`. To declare a pointer that can point at this function, we declare a pointer in place of the function name:
```cpp
// pf points to a function returning bool that takes two const string references
bool (*pf)(const string &, const string &);  // uninitialized
```
Starting from the name we are declaring, we see that pf is preceded by a `*`, so pf is a pointer. To the right is a parameter list, which means that pf points to a function. Looking left, we find that the type the function returns is bool. Thus, pf points to a function that has two const string& parameters and returns bool.

>The parentheses around *pf are necessary. If we omit the parentheses, then we declare pf as a function that returns a pointer to bool

# When to use 

When we use the name of a function as a value, the function is automatically converted to a pointer. We can write a parameter that looks like a function type, but it will be treated as a pointer:

**An important usage of function pointer is to define parameters of function type in function declaration**

```cpp
// third parameter is a function type and is automatically treated as a pointer to function
void useBigger(const string &s1, const string &s2,
               bool pf(const string &, const string &));
// equivalent declaration: explicitly define the parameter as a pointer to function
void useBigger(const string &s1, const string &s2,
               bool (*pf)(const string &, const string &));
```

When we pass a function as an argument, we can do so directly. It will be automatically converted to a pointer:
```cpp
// automatically converts the function lengthCompare to a pointer to function
useBigger(s1, s2, lengthCompare);
```

## use `decltype`

### preliminaries
```cpp
decltype(f()) sum = x; // sum has whatever type f returns
```
Here, the compiler does not call f, but it uses the type that such a call would return as the type for sum. That is, the compiler gives sum the same type as the type that would be returned if we were to call f. However, if we use
```cpp
decltype(f) ptr = f;
```
We get a function type.

### define function type and pointer to function type
As we’ve just seen in the declaration of useBigger, writing function pointer types quickly gets tedious. Type aliases, along with decltype (§ 2.5.3, p. 70), let us simplify code that uses function pointers:
```cpp
// Func and Func2 have function type
typedef bool Func(const string&, const string&);
typedef decltype(lengthCompare) Func2; // equivalent type

// FuncP and FuncP2 have pointer to function type
typedef bool(*FuncP)(const string&, const string&);
typedef decltype(lengthCompare) *FuncP2;  // equivalent type
```
Here we’ve used typedef to define our types. Both Func and Func2 are function types, whereas FuncP and FuncP2 are pointer types. It is important to note that decltype returns the function type; the automatic conversion to pointer is not done. Because decltype returns a function type, if we want a pointer we must add the * ourselves. We can redeclare useBigger using any of these types:
```cpp
// equivalent declarations of useBigger using type aliases
void useBigger(const string&, const string&, Func);
void useBigger(const string&, const string&, FuncP2);
```
Both declarations declare the same function. In the first case, the compiler will automatically convert the function type represented by Func to a pointer.

>functions usually decay to pointers to functions, for example, we can use the name of a function to assign it to another pointer to function. However, we shouldn't do it the other way around, i.e. `typedef` a function type and try to copy a function.

## returning a pointer to function

Similarly, we must write the return type as a pointer type; the compiler will not automatically treat a function return type as the corresponding pointer type.

There are three methods to return a pointer to a function:
1. `using`

The easiest way to declare a function that returns a pointer to function is by using a type alias:
```cpp
using F = int(int*, int);     // F is a function type, not a pointer
using PF = int(*)(int*, int); // PF is a pointer type
```
Here we used type alias declarations (§ 2.5.1, p. 68) to define F as a function type and PF as a pointer to function type. The thing to keep in mind is that, unlike what happens to parameters that have function type, the return type is not automatically converted to a pointer type. We must explicitly specify that the return type is a pointer type:
```cpp
PF f1(int); // ok: PF is a pointer to function; f1 returns a pointer to function
F f1(int);  // error: F is a function type; f1 can't return a function
F *f1(int); // ok: explicitly specify that the return type is a pointer to function
```
2. trailing return 
```cpp
auto f1(int) -> int (*)(int*, int);
```
3. `decltype`

If we already know which function we want to return, we can use `decltype` to simplifty writing a function pointer return typle.
```cpp
decltype(sumLength) *getFcn(const string &);
```
The only tricky part in declaring getFcn is to remember that when we apply decltype to a function, it returns a function type, not a pointer to function type. We must add a * to indicate that we are returning a pointer, not a function.
