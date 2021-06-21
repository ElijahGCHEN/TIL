>A specialization is a separate definition of the template in which one or more template parameters are specified to have particular types

Sometimes, we don't need to specialize the whole class template, because most of the generic code is still applicable. However, we may want to change the implementation of one or a few member functions. We can explicitly specialize the member function

# Syntax
```cpp
template <> //keyword `template` followed by an empty pair of angle brackets
int compare(const char* const &p1, const char* const &p2) {
  return strcmp(p1, p2);
}
```
When we define a specialization, the function parameter type(s) must match the corresponding types in a previously declared template.

The following is a example of a full specialization:
```cpp
template <>
class Ratio<double, double> {
    public:
    Ratio() : value_() {}
    template <typename N, typename D>
    Ratio(const N& num, const D& denom) :
        value_(double(num)/double(denom)) {}
    explicit operator double() const { return value_; }
    private:
    double value_;
};
```

# Partial template specialization

The simplest form of the partial template is one where some of the generic types are replaced by concrete types, but other types remain generic:

```cpp
template <typename N, typename D>
class Ratio {
    .....
};

template <typename D>
class Ratio<double, D> {
    public:
    Ratio() : value_() {}
    Ratio(const double& num, const D& denom) : value_(num/double(denom)) {}
    explicit operator double() const { return value_; }
    private:
    double value_;
};
```

## Partial specialization as pointers

```cpp
template <typename N, typename D>
class Ratio<N*, D*> {
    public:
    Ratio(N* num, D* denom) : num_(num), denom_(denom) {}
    explicit operator double() const {
        return double(*num_)/double(*denom_);
    }
    private:
    N* const num_;
    D* const denom_;
};
int i = 5; double x = 10;
auto r(make_ratio(&i, &x));    // Ratio<int*, double*>
double(r);                     // 0.5
x = 2.5;
double(r);                     // 2
```

