# Fast system calls
Modern versions of Intel x86 CPU provided *fast system call* instructions. These instructions were designed specifically to address the performance bottleneck of interrupt-driven system calls.

# Different kinds of system calls
1. Console input/output

Your application doesn't actually know where it is sending the text to. The application leverages an API to out put to `stdout`, which is managed by device drivers.

2. Memory allocation

Most applications are given **global** and **stack** memory resources when the application is first executed, along with a small **heap** of memory that the application can use when calls to functions such as `malloc()` and `free()` are made.

*calls to malloc() or free() should not be made in time-critical code, as the time it takes to execute these functions can vary greatly.*

3. File input/output

4. Networking

Sockets provide an API for programming the **Network Interface Controller (NIC)**, and support logic (for example, the TCP/IP stack) within the operating system.

5. Time

If this information is needed, the operating system will ask the device driver responsible for managing the chip what date and time it is currently storing.

High-resolution timers do not need the operating system to perform a system call because these high-resolution timers usually exist directly in the CPU, and their values can be extracted using a simple instruction.

6. Threading and process creation 

# Possible attacks to system calls

1. SYSRET

A race condition that using applications' stack rather than kernel's stack.

2. Meltdown and spectre

This attack is due to an optimization called *speculative execution*. Generally, that is done by convincing the CPU to speculatively execute an instruction that would typically cause a security violation. If that's the case, there's a gap between the CPU loading the password into the CPU's cache, and the CPU figuring out that a security violation has occurred.

# Type safety in C++

>Type safety refers to protections put in place to prevent one type from being confused with another type.

C++ provides **run-time type information (RTTI)**, which is useful especially for the `dynamic-cast<>` operator, which performs runtime type checking. This is often seen when performing the following:

1. Polymorphic type conversions.

C++ is capable of determining at runtime whether a provided pointer is being cast to the proper type.

2. Exception support.

When catching an exception, C++ uses RTTI (essentially dynamic_cast<>), to ensure that the exception being thrown is caught by the proper handler. 

# C, C++ and POSIX Standards

>Specifically, the C standard defines program linking and execution, the standard C syntax (which a number of higher-level languages, such as C++, are based on), and the C libraries that provide the ABI-to-API wrappers.

>The C libraries can be thought of as a subset of the greater POSIX standard, which defines a much larger subset of APIs, including, but not limited to, filesystem, network, and threading libraries.

>C++ standard defines the C++ syntax, program linking and execution, and the C++ libraries that provide higher-level abstractions of the C and POSIX standards.

## C
C standard is broken up into three sections:
1. Environment:  the minimum limitations the compiler must adhere to (such as the minimum number of nested if() statements that must be supported), as well as how programs are linked and started.
2. Syntax
3. Libraries

### Environment
#### How a C program starts

>A C program doesn't start with a main function. Many libraries are by default linked to the object files for us. 

Specifically, the C run-time libraries (CRT) libraries are the libraries of interest here. These libraries provide the code that is needed to bootstrap the application, including:

- Executing global constructors and destructors (as GCC supports constructors and destructors in C, even though this is not a standard C facility).
- Setting up unwinding to support exception supporting. Although this is mainly needed for C++ exceptions, which are not needed in a standard C-only application, they are still needed for linking in the set jump exception logic, a topic that will be explained in Chapter 13, Error - Handling with Exceptions. 
- Providing the `_start` function, which is the actual entry point to any C-based application using a default GCC compiler. 


Finally, all these libraries are responsible for providing the main() function with the arguments that are passed to it, as well as intercepting the return value of the main() function, and executing the exit() function on your behalf, as needed.

#### Compilation 

A compilation unit will take each cpp file and work its way through included headers to locate forward declaration of implementations of symbol signatures of used functionality in your code. In your case this involves gsl/gsl.h. If the file cannot be found in the search directories, which you can expand by specifying C_INCLUDE_PATH and or CPLUS_INCLUDE_PATH. If you omit #include <gsl/gsl_math.h>, your code will not compile as there are signatures, which cannot be found for GSL functions, which you use.

#### Linking

>Specifically, an executable is the combination of object files and libraries.

Libraries are a combination of additional object files, divided into two different types:

- Static libraries: libraries that are linked at compile time

Static libraries are nothing more than an archive of object files. 

- Dynamic libraries: libraries that are linked at load time

Dynamic libraries are more like executables without an entry point.

Once you have compiled all cpp/cc files you need to link the binary, which can be executed. The linking process consists of a search through all symbols in your .o/.obj... files and a matching of the same to what it can find in your object files and the libraries, which you have specified using for example -lgsl. If all goes well, every lookup finds an according binary implementation for your machine's hardware model (i.e. 64bit/32bit ..., Arm, Intel, ... etc). If some are not found you will find linkage errors.

It is also possible for your program to link itself while it executes during runtime as an optimization, only linking functions that are needed (a process known as lazy loading). This is done by the *ld-linux-x86-64* library, which can be found by the LDD tool.

#### Libraries

The standard C libraries that provide cross-platform APIs to wrap an operating system's ABIs. Libraries that involve system programming include `limits.h`, `stdio.h`, `time.h` and so on.

## C++

C++ standard defines the C++ syntax, program linking and execution, and the high-level C++ APIs that wrap underlying C and POSIX APIs to C++. 

### General Conventions and Concepts

*The _start() function is the actual entry point of the application, while the main() function is called by the _start() function after initialization has completed.*

Also, function names are mangled in C++. That's basically because of the function overloading feature introduced in C++.

### Language Syntax
### Libraries

For example, console input/output, threading libraries, and so on.


## POSIX Standard

>The POSIX standard defines the system call interface that the operating system must support.

Most of the system-level APIs that C and C++ provide actually execute POSIX functions, or are POSIX functions themselves. In fact, `libc` is generally considered to be a subset of the greater POSIX standard, while C++ leverages `libc` and POSIX to implement its higher-level APIs such as threading, memory management, error handling, file operations, and input/output.

### Memory Management
### FileSystems

POSIX not only defines how to read and write a file from a POSIX-compliant OS, it also defines where files should be located on the file system. Besides, it also defines the layout of the filesystem, including the common folders.

### Sockets

It's the networking API provided neither by C or C++. 

### Threading

C++ leverages the POSIX threads library (pthreads)—so, even though C++ provides a set of APIs for working with threads, in the end, POSIX threads are responsible for threading in all cases. 


# System Types for C and C++

Never assume, while system programming, that when interfacing with a system call, that your application's definition of a specific type is the same as the type the API assumes.

## Int
An `int` type is, in most cases, more efficient than using a smaller type; even if you don't need a full 4 or 8 bytes, it's still faster. Because the system usually provides the default `int` type which represents a CPU register in order to avoid possible alignment problems for RISC CPUs.

Most of these types should not be used when system programming with the exception of int.

## Floating Point Numbers
They are rarely used in system programming.

## Boolean
a Boolean using C++ on a 64 bit Intel CPU is 1 byte in size, and can take on a value of 0 or 1. It should be noted, for the same reasons as already identified, a Boolean could be 32-bits or even 64-bits, depending on the CPU architecture. On an Intel CPU, which is capable of supporting register sizes of 8 bits (that is, 1 byte), a Boolean only needs to be 1 byte in size.

A Boolean technically only needs a single bit to store its value, but rarely (if any) CPU architectures support bit-style register and memory access, meaning a Boolean typically consumes more than a single bit, and in some cases could consume as many as 64 bits. **If the size of your resulting file is important, storing a Boolean using the built-in Boolean type may not be preferred (ultimately resulting in the need for bit masking).**

## The Standard Integer types

The `stdint.h` defines the following types:
- `int8_t`, `uint8_t`
- `int16_t`, `uint16_t`
- `int32_t`, `uint32_t`
- `int64_t`, `uint64_t`

**They define the width of integer types with a specific number of bits.** No matter what the CPU architecture (CISC or RISC), operating system (32-bit or 64-bit), or mode is, these types are always the same.

**In general, if the size of the data type you are working with is important, use the standard integer types instead of the default types provided by the language.**

## Structure Packing
```cpp
#include <iostream>

struct mystruct {
    uint16_t data1;
    uint64_t data2;
};

int main()
{
    std::cout << "size: " << sizeof(mystruct) << '\n';
}

// > g++ scratchpad.cpp; ./a.out
// size: 16
```
Under the hood, the compiler is replacing the 16 bit integer with a 64 bit integer. The reason for this is the base type for C and C++ is an int, and the compiler is allowed to change a type smaller than an int with an int, even though we explicitly declared the second integer as a 16 bit integer.

```cpp
#include <iostream>

int main()
{
    int16_t s = 42;
    auto result = s + 42;
    std::cout << "size: " << sizeof(result) << '\n';
}

// > g++ scratchpad.cpp; ./a.out
// size: 4
```
The compiler represents 42 as an int, which in this case means that the system this code was compiled on is 4 bytes in size. The compiler represents 42 as an int, and int plus an `int16_t`, which results in an int, as that is the higher width type. In the previous example, we define our result variable using auto, which ensures that the resulting type reflects the type the compiler created as a consequence of this arithmetic. We could have defined result as another `int16_t`, which would have worked unless we turned on integer type conversion warnings.

we can wrap the structure with the `#pragma pack` and `#pragma pop` macros. These macros tell the compiler (since we passed a 1 to the macro, which indicates a byte) to pack the structure using a byte granularity, telling the compiler it is not allowed to make a substitution optimization. 

`alignas()` is preferred.

**Specifically, the size of a pointer depends entirely on the CPU architecture, operating system, and mode the application is running in.**

# C++ 17

## Changes to Syntax

### Initializers in if/switch statements
```cpp
#include <iostream>

int main(void)
{
    if (auto i = 42; i > 0) {
        std::cout << "Hello World\n";
    }
}

// > g++ scratchpad.cpp; ./a.out
// Hello World
```
the variable is only defined when the condition is met. That is, in the preceding example, i only exists if i is greater than 0.

### Additions to compile-time facilities
```cpp
#include <iostream>

int main(void)
{
    if constexpr (constexpr const auto i = 42; i > 0) {
        std::cout << "Hello World\n";
    }
}

// > g++ scratchpad.cpp; ./a.out
// Hello World

```

`static_assert`: Performs compile-time assertion checking. From C++17, it's OK to only have one parameter which is a bool_constexpr.

### Namespaces

It's OK to have nested namespace on the same line:
```cpp
#include <iostream>

namespace X::Y::Z 
{
    auto msg = "Hello World\n";
}

int main(void)
{
    std::cout << X::Y::Z::msg;
}

// > g++ scratchpad.cpp; ./a.out
// Hello World
```

### Structure Binding

In C++17, we can do structure binding to retrieve the results of functions like in Python:
```cpp
#include <iostream>

std::pair<const char *, int>
give_me_a_pair()
{
    return {"The answer is: ", 42};
}

int main(void)
{
    auto [msg, answer] = give_me_a_pair();
    std::cout << msg << answer << '\n';
}

// > g++ scratchpad.cpp; ./a.out
// The answer is: 42
```

`[msg, answer]`  are initialized to the results of `std::pair`.


### Inline Variables

See another file

## Changes in the Library

### `std::any`, `std::variant`, `std::optional`

`std::any{}` is capable of storing any value at any time. Special accessors are needed to retrieve the data in `std::any{}`, but they are capable of holding any value in a type-safe manner.

``std::variant`` is more like a type-safe union. A union reserves storage space for all of the types that are stored within the union at compile time (so no allocations is needed, but all of the possible types must be known at compile time).

The problem with a standard C union is that there is no way to know what type is stored at any given time. Storing both an int and a double at the same time is problematic, since using both simultaneously will lead to corruption.

With `std::variant`, this type of issue can be avoided, as `std::variant` is aware of what type it is currently storing, and attempting to access the data as a different type is not allowed (hence, `std::variant` is type-safe), for example:

```cpp
#include <iostream>
#include <variant>

int main(void)
{
std::variant<int, double> v = 42;
std::cout << std::get<int>(v) << '\n';

v = 4.2;
std::cout << std::get<double>(v) << '\n';
}

// > g++ scratchpad.cpp; ./a.out
// 42
// 4.2

```

`std::optional` is a nullable value type. It has the same interface as `unique_ptr`.

# Resource Acquisition Is Initialization (RAII)

**RAII: If a recourse is allocated, it's allocated during the construction of an object, and when the object is destroyed, the resource is released**. It leads to construction and destruction features of C+.

# The Guideline Support Library (GSL)

As stated before, the goal of the C++ Core Guidelines is to provide a set of best practices associated with programming C++. The GSL is a library designed to assist in maintaining compliance with these guidelines. In general, there are some overall themes associated with the GSL:

- Pointer ownership: The owner of a pointer is the object responsible for releasing the memory associated with a pointer.
- Expectation management 
- No pointer arithmetic: It can only be done by well-tested support libraries such as `std::advance`.

# Contracts

>C++ contract documents a contract between the author of an API and the user of the API, it also provides compile-time and runtime validation of that contract. 

The GSL provides a library-based implementation of C++ contracts by providing the expects() and ensures() macros, for example:
```cpp
#define GSL_THROW_ON_CONTRACT_VIOLATION
#include <gsl/gsl>
#include <iostream>

int main(void)
{
    try {
        Expects(false);
    }
    catch(const gsl::fail_fast &e) {
        std::cout << "exception: " << e.what() << '\n';
    }
}

// > g++ scratchpad.cpp; ./a.out
// exception: GSL: Precondition failure at ...
```

