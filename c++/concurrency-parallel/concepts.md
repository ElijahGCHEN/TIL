>Using multiple threads isn't complicated in and of itself; the complexity lies in designing the code so that it behaves as intended.
# Why use concurrency
## For seperation of concerns
By grouping related bits of code together and keeping unrelated bits of code apart, you can make your programs easier to understand and test, and less likely to contain bugs.

## For performance
### Task parallelism
Deviding a task into parts and run each in parallel, reducing the total runtime.

### Data parallelism
Each thread performs the same operation on different parts of the data.

# Concurrency v.s. parallelism
> Parallelism is much more performance-oriented. People talk about parallelism when their primary concern is taking advantage of the available hardware to increase the performance of bulk data processing, whereas people talk about concurrency when their primary concern is seperation of concerns, or responsiveness.

# Simple example
```cpp
#include <iostream>
#include <thread>
void hello ()
{
  std::cout<<"Hello Concurrent World\n";
}
int main()
{
  std::thread t(hello);
  t.join();
}
```
The code for writing the message has been moved to a seperate function. This is because every thread has to have an initial function, where the new thread of execution begins. For the initial thread in an application, this is `main()`, but for every other thread it's specified in the constructor of a `std::thread` object -- in this case, the `std::thread` object named `t` has the new `hello()` function as its initial function.

This is the next difference: rather than writing directly to standard output or calling `hello()` from `main()`, this program launches a new thread to do it, bringring the thread count to two -- the initial thread that starts at `main()` and the new thread that starts at `hello()`.

After the new thread has been launched, the initial thread continues execution. If it didn't wait for the new thread to finish, it would merrily continue to the end of `main()` and end the program - possibly before the new thread had a chance to run. This is why the call to `join()` is there - this causes the calling thread (in `main()`) to wait for the thread associated with the `std::thread` object, in thsi case, `t`.

