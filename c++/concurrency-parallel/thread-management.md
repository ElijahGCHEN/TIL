- If you don’t decide to call `join()` or `detach()` before the std::thread object is destroyed, then your program is terminated (the std::thread destructor calls std::terminate())

## Transferring ownership of a thread
many resource-owning types in the C++ Standard Library, such as `std::ifstream` and `std::unique_ptr`, are movable but not copyable, and `std::thread` is one of them.

The example shows the creation of two threads of execution and the transfer of ownership of those threads among three std::thread instances, t1, t2, and t3:
```cpp
void some_function();
void some_other_function();
std::thread t1(some_function);
std::thread t2=std::move(t1);
t1=std::thread(some_other_function);
std::thread t3;
t3=std::move(t2);
t1=std::move(t3);          1
```
- 1 This assignment will terminate the program!  In this case t1 already had an associated thread (which was running some_other_function), so `std::terminate()` is called to terminate the program.

