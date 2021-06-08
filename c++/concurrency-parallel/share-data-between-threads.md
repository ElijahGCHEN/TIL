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
It makes sure that other threads have to wait for the mutex to unlock before they can process the data protected by the mutex. However, it's far from enough by only putting mutex in every member function.

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

1. Read the sections of the book describing static members
2. Pick and understand share_ptr itself
3. How lock makes the example thread-safe.

