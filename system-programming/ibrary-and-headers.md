# Library Header Files v.s. Libraries

**Header files**: The files that tell the compiler how to call some functionality (without knowing how the functionality actually works) are called header files. They contain the function prototypes. They also contain data types and constants used with the libraries. We use `#include` to use these header files in programs. These files end with `.h` extension.

**Library** is the place where the actual functionality is implemented i.e. they contain function body. 

In simple terms a header file is like a visiting card and libraries are like a real person, so we use visiting card (header file) to reach to the actual person (library).

# Static/Dynamic Libraries v.s. Header-only-library

> In the context of the C or C++ programming languages, a library is called header-only if the full definitions of all macros, functions and classes comprising the library are visible to the compiler in a header file form. Header-only libraries do not need to be separately compiled, packaged and installed in order to be used. All that is required is to point the compiler at the location of the headers, and then #include the header files into the application source.

There are situations when a header-only library is the only option, for example when dealing with templates.

Having a header-only library also means you don't have to worry about different platforms where the library might be used. When you separate the implementation, you usually do so to hide implementation details, and distribute the library as a combination of headers and libraries (lib, dll's or .so files). These of course have to be compiled for all different operating systems/versions you offer support.

The issue with these types of libraries is that they have to play fancy tricks to include global variables in the library itself. 

After C++17, it's possible to only declaimer the global variable using `inline`:
```cpp	
#include <iostream>

inline auto msg = "Hello World\n";

int main(void)
{
    std::cout << msg;
}

// > g++ scratchpad.cpp; ./a.out
// Hello World
```