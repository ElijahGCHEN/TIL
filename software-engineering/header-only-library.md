# Static/Dynamic Libraries v.s. Header-only-library

> In the context of the C or C++ programming languages, a library is called header-only if the full definitions of all macros, functions and classes comprising the library are visible to the compiler in a header file form. Header-only libraries do not need to be separately compiled, packaged and installed in order to be used. All that is required is to point the compiler at the location of the headers, and then #include the header files into the application source.

There are situations when a header-only library is the only option, for example when dealing with templates.

Having a header-only library also means you don't have to worry about different platforms where the library might be used. When you separate the implementation, you usually do so to hide implementation details, and distribute the library as a combination of headers and libraries (lib, dll's or .so files). These of course have to be compiled for all different operating systems/versions you offer support.
