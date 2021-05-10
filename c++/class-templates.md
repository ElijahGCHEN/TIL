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
