>A specialization is a seperate definition of the template in which one or more template parameters are specified to have particular types

# Syntax
```cpp
template <> //keyword `template` followed by an empty pair of angle brackets
int compare(const char* const &p1, const char* const &p2) {
  return strcmp(p1, p2);
}
```
When we define a specialization, the function parameter type(s) must match the corresponding types in a previously declared template.
