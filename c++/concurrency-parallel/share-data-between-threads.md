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

