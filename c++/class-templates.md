Take the following code as an example:
```cpp
template <typename CreateObject> class BaseFactory {
private:
  /*! std::map from the object type identifier (a string) to its callback function */
  typedef std::map<std::string, CreateObject> CallbackMap;
  /*! std::pair of an object type identifier and a callback function */
  typedef  typename CallbackMap::value_type CallbackPair;
  /*! const iterator */
  typedef  typename CallbackMap::const_iterator CallbackConstIter;
```
The reason why `typedef  typename CallbackMap::value_type CallbackPair;` need the combination of `typedef` and `typename` is that the compiler is not able to peer inside of the template type until it has been instantiated. So in order for this to be valid syntax the programmer must clarify whether value_type is a type, i.e., with typename or not (its a member variable or method).


Class template need to be implemented in the header file. When instantiating a template, the compiler creates a new class with the given template argument. For example:
```cpp
template<typename T>
struct Foo
{
    T bar;
    void doSomething(T param) {/* do stuff using T */}
};

// somewhere in a .cpp
Foo<int> f; 
```
When reading this line, the compiler will create a new class (let's call it FooInt), which is equivalent to the following:
```cpp
struct FooInt
{
    int bar;
    void doSomething(int param) {/* do stuff using int */}
}
```
Consequently, the compiler needs to have access to the implementation of the methods, to instantiate them with the template argument (in this case int). If these implementations were not in the header, they wouldn't be accessible, and therefore the compiler wouldn't be able to instantiate the template.

On the contrary, function templates are not constrained by this rule. That is because the type of a instantiated function template can be anticipated by the supplied variables.

Because of this reason, it's valid to have rvalue reference in class template because it's initialized immediately with the speficied data type, thus making it impossible to bind a regular lvalue to a rvalue reference. For example:
```cpp
#include <iostream>

template <typename T>
class test
{
public:
    test(T&& t)
    {
    }
};

template <typename T>
void succeed(T&& t)
{
}

int main()
{
    int i = 1;
    test<int> t(i);    // failed to compile
    succeed(i);        // OK
    return 0;
}
```
In the function version you don't tell the compiler what T is, but instead you expect the compiler to deduce it. In situations like yours the language is deliberately designed to deduce T as int & (note: not as int, but rather as int &). Once T is deduced as int &, the so called "reference collapsing" rules lead to function parameter type T && becoming int & - an ordinary lvalue reference. This parameter can successfully bind to lvalue argument i.

>In succeed, T&& t is a forwarding reference, not an rvalue reference. But in test, it is an rvalue reference. A forwarding reference happens only when the parameter is T&&, and T is a template parameter of that function. A forwarding reference may bind to both lvalues and rvalues.
