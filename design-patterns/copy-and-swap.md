>Exception-safe program: throwing an exception should never leave the program in an undefined state. For example, if an operation causes an error, the resources already consumed by the operation in progress should be released. 



Guidelines imposed on btoh standard `std::swap` and `swap` member functions:
1. Classes that support swap should implement `swap` member functions that perform the operation in constant time.
2. A free-standing `swap` non-member function for all swappable types.
3. Swapping two objects should not throw exceptions or otherwise fail.

# Standard non-member swap

1. For STL containers, `swap` calls the member function `swap()`.
2. For other types without customized implementation, a temporary object is used for the swap. In C++11, it is done by using move assignment and move copying. If move constructors are not available, copy constructors are used. Note that if the move constructors are deleted, `swap` will not compile for it.


# Store all elements of a vector in a new vector

```cpp
void transmogrify(const std::vector<C>& in, std::vector<C>& out) {
    std::vector<C> tmp;
    tmp.reserve(in.size());
    for (const auto& x : in) {
        tmp.push_back(transmogrify(x));
    }
    out.swap(tmp);        // Must not throw!
}
```

# How to implement a swap 

## Declare 

```cpp
class C {
    public:
    void swap(C& rhs) noexcept;
};
```
The `noexecpt` should only be included if a no-throw guarantee can indeed be given. 

## Implementation

### Swapping one at a time

This delegates the problem of swapping to objects to their contained types, which, if they follow the pattern, will eventually end up swapping the built-in types that everything is made of. 


If the data member has a `swap` member function, we should use it. Otherwise we should call the free-standing `swap`.

### Pimpl idion/handle-body idiom

**It is mainly used to minimize compilation dependencies and avoid exposing the implementation of the class in the header file.**

In this idiom, the entire declaration of a class in the header file consists of all the necessary public member functions, plus a single pointer that points to **the actual implementation**. The pointer to implementation data member is often called **p_impl** or **pimpl**, hence the name of the idiom.

[Examples from here](https://qiita.com/ashdik/items/b6b9924113f7e8d531cf)

#### Without Pimpl

*Hoge.h*
```cpp
// Hoge.h
class Hoge
{
public:
    Hoge();
    ~Hoge();

private:
    void printHoge();
};
```

*Hoge.cpp*
```cpp
#include "Hoge.h"

Hoge::Hoge()
{
    this->printHoge();
}

Hoge::~Hoge() {}

void Hoge::printHoge()
{
    CCLOG("hoge");
}
```

#### With Pimpl
*Hoge.h*
```cpp
class Hoge
{
public:
    Hoge();
    ~Hoge();

private:
    class Impl;
    std::unique_ptr<Impl> hoge;   ...Ⅰ
};
```

*Hoge.cpp*
```cpp
#include "Hoge.h"

class Hoge::Impl
{
public:
    void printHoge();
};                                ...Ⅱ

Hoge::Hoge()
    : hoge(new Hoge::Impl())      ...Ⅲ
{
    this->hoge->printHoge();      ...Ⅳ
}

Hoge::~Hoge() {}

void Hoge::Impl::printHoge()      ...Ⅴ
{
    CCLOG("hoge");
}
```

# Using Swap Correctly
>In particular, any type with a member function swap should also provide a non-member function overload that calls that member function.

```cpp
namespace std {
    void swap(N::C& lhs, N::C& rhs) noexcept { lhs.swap(rhs); }
}
```

