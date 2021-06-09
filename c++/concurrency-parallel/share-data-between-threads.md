>In general, a lock should be held for only the minimum possible time needed to perform the required operations.

## Solution to avoid problematic data race condition
1. Wrap the data structure 
2. Lock-free programming
3. Handle updates as transactions

## Make use of **mutex** (mutual exclusion) and **lock_guard**

>`std::lock_guard`:it locks the supplied mutex on construction and unlocks it on destruction, ensuring a locked mutex is always correctly unlocked.

```cpp
class data_wrapper
{
private:
    some_data data;
    std::mutex m;
public:
    template<typename Function>
    void process_data(Function func)
    {
        std::lock_guard<std::mutex> l(m);
        func(data);
    }
};
```
It makes sure that other threads have to wait for the mutex to unlock before they can process the data protected by the mutex. 

Although there are occasions where this use of global variables is appropriate, in the majority of cases it’s common to group the mutex and the protected data together in a class rather than use global variables.

However, it's far from enough by only putting mutex in every member function.

## Pitfall #1: Modifying from other places

This happens when the references && pointers are obtained by other callers.

**Don’t pass pointers and references to protected data outside the scope of the lock, whether by returning them from a function, storing them in externally visible memory, or passing them as arguments to user-supplied functions.**

## Pitfall #2: mutex individually 

Problematic race conditions in interfaces arise because of locking at too small a granularity; the protection doesn’t cover the entirety of the desired operation.

```cpp
stack<int> s;
if(!s.empty())                  1
{
    int const value=s.top();    2
    s.pop();                    3
    do_something(value);
}
```
It can happens when there's another thread calling `pop()` in between the call to `empty()` and the call to `top()`. This leads to a an undefined behaviour.

Another possibility is that there's another thread calling `pop()` in between the call to **2** and **3**. In this case, one of the two values on the stack is discarded without ever having been read, whereas the other is processed twice.

Possible solutions:
1. Pass in a reference.

pass a reference to a variable in which you want to receive the popped value as an argument.
- Pros: Works in most of the situations.
- Cons: Requiring the calling code to construct aninstance of the stack's value type prior to the call which could be impractical because either it's expensive or no enough information. Also, the stored type need to be assignable, which in many user-defined types are not true.

2. Require a no-throw copy constructor or move constructor

3. Return a pointer to the popped item

## Pitfall #3: Deadlock

Deadlocks happen where you need to acquire two or more locks together.

This can be dealt with `std::lock` -- a function that can lock two or more mutexes at once without risk of deadlock.

 The std::adopt_lock parameter is supplied in addition to the mutex to indicate to the std::lock_guard objects that the mutexes are already locked, and they should adopt the ownership of the existing lock on the mutex rather than attempt to lock the mutex in the constructor.

```cpp
class some_big_object;
void swap(some_big_object& lhs,some_big_object& rhs);
class X
{
private:
    some_big_object some_detail;
    std::mutex m;
public:
    X(some_big_object const& sd):some_detail(sd){}
    friend void swap(X& lhs, X& rhs)
    {
        if(&lhs==&rhs)
            return;
        std::lock(lhs.m,rhs.m);                                     1
        std::lock_guard<std::mutex> lock_a(lhs.m,std::adopt_lock);  2
        std::lock_guard<std::mutex> lock_b(rhs.m,std::adopt_lock);  3
        swap(lhs.some_detail,rhs.some_detail);
    }
};
```


C++17 provides additional support for this scenario, in the form of a new RAII template, std::scoped_lock<>. This is exactly equivalent to std::lock_guard<>, except that it is a variadic template.
```cpp
void swap(X& lhs, X& rhs)
    {
        if(&lhs==&rhs)
            return;
        std::scoped_lock guard(lhs.m,rhs.m);       1
        swap(lhs.some_detail,rhs.some_detail);
    }
```
the C++17 implicit class template parameter deduction mechanism will choose the correct mutex types from the types of the objects passed to the constructor at object 1. This line is equivalent to the fully specified version:
```cpp
std::scoped_lock<std::mutex,std::mutex> guard(lhs.m,rhs.m);
```
### Other tips for avoiding deadlocks

1. Don’t wait for another thread if there’s a chance it’s waiting for you.
2. Don’t acquire a lock if you already hold one. 

## An example: `share_ptr`


```cpp
#include <iostream>
#include <memory>
#include <thread>
#include <chrono>
#include <mutex>
 
struct Base
{
    Base() { std::cout << "  Base::Base()\n"; }
    // Note: non-virtual destructor is OK here
    ~Base() { std::cout << "  Base::~Base()\n"; }
};
 
struct Derived: public Base
{
    Derived() { std::cout << "  Derived::Derived()\n"; }
    ~Derived() { std::cout << "  Derived::~Derived()\n"; }
};
 
void thr(std::shared_ptr<Base> p)
{
    std::this_thread::sleep_for(std::chrono::seconds(1));
    std::shared_ptr<Base> lp = p; // thread-safe, even though the
                                  // shared use_count is incremented
    {
        static std::mutex io_mutex;
        std::lock_guard<std::mutex> lk(io_mutex);
        std::cout << "local pointer in a thread:\n"
                  << "  lp.get() = " << lp.get()
                  << ", lp.use_count() = " << lp.use_count() << '\n';
    }
}
 
int main()
{
    std::shared_ptr<Base> p = std::make_shared<Derived>();
 
    std::cout << "Created a shared Derived (as a pointer to Base)\n"
              << "  p.get() = " << p.get()
              << ", p.use_count() = " << p.use_count() << '\n';
    std::thread t1(thr, p), t2(thr, p), t3(thr, p);
    p.reset(); // release ownership from main
    std::cout << "Shared ownership between 3 threads and released\n"
              << "ownership from main:\n"
              << "  p.get() = " << p.get()
              << ", p.use_count() = " << p.use_count() << '\n';
    t1.join(); t2.join(); t3.join();
    std::cout << "All threads completed, the last one deleted Derived\n";
}
```
## Other locks
### `unique_lock`
`std::unique_lock` provides `lock()`, `try_lock()`, and `unlock()` member functions.

You can pass std::adopt_lock as a second argument to the constructor to have the lock object manage the lock on a mutex

There are cases where std::unique_lock is a better fit for the task at hand because you need to make use of the additional flexibility that allows you to don't always own the mutex that it's associated with.

One possible use is to allow a function to lock a mutex and transfer ownership of that lock to the caller, so the caller can then perform additional actions under the protection of the same lock. The following code snippet shows an example of this; the get_lock() function locks the mutex and then prepares the data before returning the lock to the caller:
```cpp
std::unique_lock<std::mutex> get_lock()
{
    extern std::mutex some_mutex;
    std::unique_lock<std::mutex> lk(some_mutex);
    prepare_data();
    return lk;                                      1
}
void process_data()
{
    std::unique_lock<std::mutex> lk(get_lock());    2
    do_something();
}
```

