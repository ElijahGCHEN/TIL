>Exception-safe program: throwing an exception should never leave the program in an undefined state. For example, if an operation causes an error, the resources already consumed by the operation in progress should be released. 

Guidelines imposed on btoh standard `std::swap` and `swap` member functions:
1. Classes that support swap should implement `swap` member functions that perform the operation in constant time.
2. A free-standing `swap` non-member function for all swappable types.
3. Swapping two objects should not throw exceptions or otherwise fail.

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

In this idiom, the entire declaration of a class in the header file consists of all the necessary public member functions, plus a single pointer that points to **the actual implementation**. 