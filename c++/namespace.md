- Namespaces can be discontiguous

## Inline namespace
Unlike ordinary nested namespaces, names in an inline namespace can be used as if they were direct members of the enclosing namespace. That is, we need not qualify names from an inline namespace by their namespace name. We can access them using only the name of the enclosing namespace.

Inline namespaces are often used when code changes from one release of an application to the next. For example, we can put all the code from the current edition of the Primer into an inline namespace. Code for previous versions would be in non-inlined namespaces.

## Unname namespace
 Variables defined in an unnamed namespace have static lifetime: They are created before their first use and destroyed when the program ends.
 
 Unlike other namespaces, an unnamed namespace is local to a particular file and never spans multiple files.
 
 Having something in an anonymous namespace means it's local to this translation unit (.cpp file and all its includes) this means that if another symbol with the same name is defined elsewhere there will not be a violation of the One Definition Rule (ODR).
 
## `using` declaration and `using` directive
 
using declaration Mechanism to inject a single name from a namespace into the current scope:
```cpp
using std::cout;
```
makes the name cout from the namespace std available in the current scope. The name cout can subseuquently be used without the std:: qualifier.

using directive Declaration of the form
```cpp
using NS;
```
makes all the names in the namespace named NS available in the nearest scope containing both the using directive and the namespace itself.

We should generally avoid using `using` directive, but one common usage is to use `using` directive in the implementing files of the namespace itself.

## Argument-dependent lookup and parameters of Class type

We can directly access the output operator because there is an important exception to the rule that names defined in a namespace are hidden. When we pass an object of a class type to a function, the compiler searches the namespace in which the argument’s class is defined in addition to the normal scope lookup. This exception also applies for calls that pass pointers or references to a class type.

## Overloading and namespaces

A using directive lifts the namespace members into the enclosing scope. If a namespace function has the same name as a function declared in the scope at which the namespace is placed, then the namespace member is added to the overload set:
```cpp
namespace libs_R_us {
    extern void print(int);
    extern void print(double);
}
// ordinary declaration
void print(const std::string &);
// this using directive adds names to the candidate set for calls to print:
using namespace libs_R_us;
// the candidates for calls to print at this point in the program are:
//   print(int) from libs_R_us
//   print(double) from libs_R_us
//   print(const std::string &) declared explicitly
void fooBar(int ival)
{
    print("Value: "); // calls global print(const string &)
    print(ival);      // calls libs_R_us::print(int)
}
```
