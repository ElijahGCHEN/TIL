C++ programmers use `assert` and `NDEBUG` to conditionally execute debugging code. The idea is that debugging code will only by executed (possibly under debugging mode) when the program is under development. After the program is finished and ready to be deployed, the debugging mode is off.

# `assert`

>`assert` is a preprocessor macro. This means that a `assert` expression is like an inline function which will be replaced by its contents at compiling time (preprocessing is managed by the compiler).

The `assert` macro is often used to check for conditions that "cannot happen". It can be useful as an aid in getting a program debugged but should not be used to substitute for run-time logic checks or error checking that the program should do.


## Syntax

```cpp
assert(expr);
```
- If the *expr* is false, the `assert` writes a message and **TERMINATES** the program.
- If the *expr* is true, the `assert` **does nothing**.



# `NDEBUG` preprocessor variable 

The behavior of `assert`  depends on the staturs of a preprocessor variable named `NDEBUG`, which is short for "No Debug". If `NDEBUG` is defined, the `assert` does nothing. By default, `NDEBUG` is not defined, so by default, `assert` performs a run-time check.

We can "turn off" debugging by providing a `#define`  to define `NDEBUG`. Alternatively, most compilers provide a command-line option that let's define preprocessor variables:
```
$ CC -D NDEBUG main.C  # use /D with the Microsoft compiler
```

# Conditionally run some code

We introduced how to check conditions that "cannot happen". Here we introduce how to execute code that only executed under certain conditions (for example, the debugging mode is on).
```cpp
#include <iostream>

#ifdef NDEBUG
constexpr auto g_ndebug = true;
#else
constexpr auto g_ndebug = false;
#endif

int main()
{
    if constexpr (!g_ndebug) {
        std::cout << "The answer is: " << 42 << '\n';
    }
}
```

Notice here that in C++17 the if statement can execute constexpr now.

## More advanced example
```cpp
#include <iostream>

#ifdef DEBUG_LEVEL
constexpr auto g_debug_level = DEBUG_LEVEL;
#else
constexpr auto g_debug_level = 0;
#endif

#ifdef NDEBUG
constexpr auto g_ndebug = true;
#else
constexpr auto g_ndebug = false;
#endif

#define console std::cout << __FILE__ << " [" << __LINE__ << "]: "

template <std::size_t LEVEL>
constexpr void debug(void(*func)()) {
    if constexpr (!g_ndebug && (LEVEL <= g_debug_level)) {
        func();
    };
}

int main()
{
    debug<0>([] {
        console << "The answer is: " << 42 << '\n';
    });
}
```

Here a macro `console` is defined to print the file and line information, which are represented by variables `__FILE__` and `__LINE__` created by the compiler, before print the actual contents.

The `debug` uses a function template to supply the level of debug, and take one function as the parameter. Notice that `void(*func)()` here is a pointer pointing to a function with return value void and no parameter. 