>Exception-safe program: throwing an exception should never leave the program in an undefined state. For example, if an operation causes an error, the resources already consumed by the operation in progress should be released. 

Guidelines imposed on btoh standard `std::swap` and `swap` member functions:
1. Classes that support swap should implement `swap` member functions that perform the operation in constant time.
2. A free-standing `swap` non-member function for all swappable types.
3. Swapping two objects should not throw exceptions or otherwise fail.

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