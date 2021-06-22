# Non-ownership

This kind of code just does its work on objects that were created by someone else earlier and will be deleted by someone else later.

```cpp
void Transmogrify(Widged* w) {        // I will not delete w
    ...
}
void MustTransmogrify(Widget& w) {    // Neither will I
    ...
}
class WidgetProcessor {
    public:
    WidgetProcessor(Widget* w) : w_(w) {}
    ...
    Widget* w_;                        // I do not own w
};
```
**Non-owning access to an object should be granted by using raw pointers or references.**


Another issue to discuss here is the use of pointers versus references. As a matter of syntax, the reference is basically a pointer that cannot be NULL (or nullptr) and cannot be left uninitialized. It is tempting to adopt a convention that any pointer passed to a function may be NULL and must, therefore, be checked, and any function that cannot accept a NULL pointer must instead take a reference.

# Exclusive Ownership

>Exclusive ownership: the code creates an object and will delete it later. The task of deletion will not be delegated to someone else, as neither an extension of the lifetime or the object is permitted.

1. Stack variable
```cpp
void Work() {
    Widget w;
    Transmogrify(w);
    Draw(w);
}
```
By using stack variables, all variable created are in the scope of the creator function.

2. Unique Pointer

 It is mainly used in cases where an object cannot be created on the stack but must be allocated on the heap. Heap allocation often happens when the ownership is shared or transferred—after all, the stack-allocated object will be deleted at the end of the containing scope; there is no way around it. If we need to keep the object alive for longer, it has to be allocated somewhere else. But we are talking about exclusive ownership here, the one that is not shared or given away. The other reason to create objects on the heap is that the size or type of the object may not be known at compile time. This usually happens when the object is polymorphic—a derived object is created, but the base class pointer is used. 


# Transferring of Exclusive Ownership

```cpp
std::unique_ptr<Widget> WidgetFactory() {
    Widget* new_w = new Widget;
        ...
    return std::unique_ptr<Widget>(new_w);
}
std::unique_ptr<Widget> w(WidgetFactory());
```
Here the move copy constructor is called to take the ownership of the unique pointer created by the factory function.

When the resource needs to be modified by a non-owing function, the `get` function need to be called:
```cpp
std::unique_ptr<Widget> w(WidgetFactory());
Transmogrify(w.get());
```

When the resource needs to be hand off to functions that take exclusive ownership, the functions **must be declared to take the parameters by the rvalue reference T&&**

```cpp
void Consume(Widget&& w) {}
Widget w;
Consume(std::move(w));
```
## Move and smart pointers
Only one std::unique_ptr instance can point to a given object at a time, and when that instance is destroyed, the pointed-to object is deleted. The move constructor and move assignment operator allow the ownership of an object to be transferred around between `std::unique_ptr` instances. Such a transfer leaves the source object with a NULL pointer.

For example, 
```cpp
std::unique_ptr p1(someClass);
std::unique_ptr p2 = p1.move();
```
This will make `p1` as a NULL pointer.


# Shared Ownership

>Shared ownership is often misused, or over-used, for example a function was passed a shared pointer to an object it did not need to own.

The most common valid applications of shared ownership are at a low level, inside data structures such as lists, trees, and more. A data element may be owned by other nodes of the same data structure, by any number of iterators currently pointing to it, and, possibly, by some temporary variables inside data structure member functions that operate on the entire structure or a part of it (such as rebalancing a tree).

Also, shared pointer is useful for creating thread-safe programs.



