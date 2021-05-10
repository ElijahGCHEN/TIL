# Using `target_compile_options`
Syntax:
```cmake
target_compile_options(${library target name}
   ${Visibility level}
   ${flags}
)
```

# Flags

- -fPIC
>Position Independent Code means that the generated machine code is not dependent on being located at a specific address in order to work.

- -Wall
>Generate warnings like "unused variable 'i'"

- -Wextra
>This enables some extra warning flags that are not enabled by `-Wall`. It also prints warning messages for the following cases:
>- A pointer is compared against integer zero with <, <=, >, or >=.
>- (C++ only) An enumerator and a non-enumerator both appear in a conditional expression.
>- (C++ only) Ambiguous virtual bases.
>- (C++ only) Subscripting an array that has been declared register.
>- (C++ only) Taking the address of a variable that has been declared register.
>- (C++ only) A base class is not initialized in the copy constructor of a derived class.

- -03
>Maximum optimization. When debugging is enabled, this option typically gives a poor debug view. ARM recommends debugging at lower optimization levels.

# Visibility Levels
- With the `PRIVATE` attribute, compile options will only be applied to the given target and not to other targets comsuming it.
- With the `INTERFACE` attribute, compile options on a given target will only be applied to targets consuming it.
- With the `PUBLIC` attribute, compile options will be applied to the given target and all other targets consuming it.
