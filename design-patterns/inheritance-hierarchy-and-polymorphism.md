#Public Inheritance

**If we cannot promise that an instance of a derived class can inherit all contracts (public interface) of the base class and be used interchangeably as an instance of the base class, we should not inherit from the base class.**

Similarly, a penguin isn't a bird in C++ if the bird interface includes flying. The correct design for such cases usually includes a more abstract base class, *Bird*, that doesn't make any promises that at least one derived class can't keep (for example, a *Bird* object doesn't make a guarantee that it can fly).

## Pointers

A conversion from a pointer to a derived class into a pointer to the base class is implicit:
```cpp
class Base { ... };
class Derived : public Base { ... };
Derived* d = new Derived;
Base* b = d;    // Implicit conversion
```

However we need to use `static_cast` or `dynamic_cast` to cast down to an inherited class from a pointer to the base class:
```cpp
Base* b = new Derived;    // *b is really Derived
Derived* d = b;           // Does not compile, not implicit
Derived* d = static_cast<Derived*>(b);    // Explicit conversion
```

It's worth noting that `dynamic_cast<>()` is preferred because it can check if the casted object is really a derived type object at runtime.
```cpp
class Base { ... };
class Derived : public Base { ... };
Base* b1 = new Derived;    // Really Derived
Base* b2 = new Base;       // Not Derived
Derived* d1 = dynamic_cast<Derived*>(b1);    // Succeeds
Derived* d2 = dynamic_cast<Derived*>(b2);    // d2 == nullptr
```

# Private Inheritance

>When inheriting privately, the derived classes don't extend the public interface of the base class -- all base class methods become private in the derived class.

# Runtime Polymorphism

>Note: When overriding a virtual function, it isn't required to repeat the virtual keyword. A virtual function in the base class is still considered as a virtual function in a 3rd level derived class even if the virtual keyword is not repeated in the direct derived class when implementing the virtual function.
>On the other hand, the *override* keyword is also optional, but it can be used to refrain from accidentally overloading the function but not correctly overriding the virtual function.


# Multiple Inheritance

>What happens if both interfaces define a method with the same name? If this method isn't virtual, then an attempt to invoke it on the derived class is ambiguous, and the program doesn't compile.




