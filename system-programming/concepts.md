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
- Providing the _start function, which is the actual entry point to any C-based application using a default GCC compiler.


Finally, all these libraries are responsible for providing the main() function with the arguments that are passed to it, as well as intercepting the return value of the main() function, and executing the exit() function on your behalf, as needed.

#### Linking

>Specifically, an executable is the combination of object files and libraries.

Libraries are a combination of additional object files, divided into two different types:

- Static libraries: libraries that are linked at compile time

Static libraries are nothing more than an archive of object files. 

- Dynamic libraries: libraries that are linked at load time

Dynamic libraries are more like executables without an entry point.

It is also possible for your program to link itself while it executes during runtime as an optimization, only linking functions that are needed (a process known as lazy loading). This is done by the *ld-linux-x86-64* library, which can be found by the LDD tool.

#### Libraries

Libraries that involve system programming include `limits.h`, `stdio.h`, `time.h` and so on.

## C++

### General Conventions and Concepts

The _start() function is the actual entry point of the application, while the main() function is called by the _start() function after initialization has completed.

Also, function names are mangled in C++. That's basically because of the function overloading feature introduced in C++.

### Language Syntax
### Libraries

For example, console input/output, threading libraries, and so on.

