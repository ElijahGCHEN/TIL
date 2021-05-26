Passing arguments to the callable object or function is fundamentally as simple as passing additional arguments to the `std::thread` constructor. 

## Supply a pointer 
But it's important to bear in mind that by default, the arguments are copied into internal storage, where they can be accessed by the newly creaated thread of execution, and then passed to the callable object or function as **rvalues** as if they were temporaries. This is done even if the corresponding parameter in the function is expecting a reference. Here's an example:
```cpp
void f(int i, std::string const& s);
std::thread t(f,3,"hello");
```
This creates a new thread of execution associated with `t`, which calls `f(3, "hello")`. Note that even though `f` takes a `std::string` as the second parameter, the string literal is passed as a `char const*` and converted to a `std::string` only in the context of the new thread. This is particularly important when the argument supplied is a pointer to an automatic variable, as follows:
```cpp
void f(int i, std::string const& s);
void oops(int some_param)
{
  char buffer[1024];
  sprintf(buffer, "%i", some_param);
  std:: thread t(f,3,buffer); // It should be t(f,3,std::string(buffer));
  t.detach();
}
```
The problem is that you were relying on the implicit conversion of the pointer to the buffer into the `std::string` object expected as a function parameter, but this conversion happens too late because the `std::thread` constructor copies the supplied values as is, without converting to the expected argument type.

## Supply an object, want a non-const reference
This won't compile.
```cpp
void update_data_for_widget(widget_id w,widget_data& data);
void oops_again(widget_id w)
{
    widget_data data;
    std::thread t(update_data_for_widget,w,data);
    display_status();
    t.join();
    process_widget_data(data);
}
```
Although `update_data_for_widget` expects the second parameter to be passed by reference, the `std::thread` constructor doesn't know that; it's oblivious to the types of the arguments expeted by the function and blindly copies the supplied values. But the internal code passes copied arguments as rvalues in order to work with move-only types., and will thus try to call `update_data_for_widget` with an rvalue. This will fail to compile because you can't pass as rvalue to a function that expects a non-const reference. 

You need to wrap the arguments that need to be references in `std::ref`.
```cpp
std::thread t(update_data_for_widget, w, std::ref(data));
```
>**std::ref**: Function templates ref and cref are helper functions that generate an object of type std::reference_wrapper, using template argument deduction to determine the template argument of the result.
