# Syntax

- `foreach`
```cmake
foreach($VARIABLE IN LISTS $LIST_NAME)
  ...
endforeach()
```
There are 3 more ways to use `foreach()`:
1. `foreach(loop_var arg1 arg2 ...)`
2. `foreach(loop_var RANGE total)`
3. `foreach(loop_var IN ITEMS)`


- `set_source_files_properties`
```cmake
set_source_files_properties(file PROPERTIES property value)
```
It sets the property to the passes value for the given file.

- `get_source_files_properties`
```cmake
get_source_files_properties(VAR file property)
```
It retrieves the value of the desired property for the given file and stores it in the CMake `VAR` variable.

- list

Lists are semicolon-separated groups of strings. A list can be created either by the `list` or the `set` commands. For example:
```cmake
set(var a b c d e)
OR
list(APPEND a b c d e)
```
