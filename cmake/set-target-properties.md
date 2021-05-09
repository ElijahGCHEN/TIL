# Setting the standard for the language

## Syntax
```cmake
set_target_properties(TARGET
  PROPERTIES
    CXX_STANDARD 14
    CXX_EXTENSIONS OFF
    CXX_STANDARD_REQUIRED ON
    POSITION_INDEPENDENT_CODE 1
)
```

## Perperties
- CXX_STANDARD mandates the standard that we would like to have.
- CXX_EXTENSIONS tell CMake to only use compiler flags that will enable the ISO C++ standard, without compiler-specific extensions.
- CXX_STANDARD_REQUIRED speficies that the version of the standard chosen is required. If this version is not available, CMake will stop configuration with an error.
