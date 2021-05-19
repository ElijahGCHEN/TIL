## Explaination

### #define directives
The `#define` directives define the *identifier* as macro, that is instruct the compiler to replace all successive occurrences of *identifier* with *replacement-list*, which can be optionally additionally processed. If the identifier is already defined as any type of macro, the program is ill-formed unless the definitions are identical.

### Object-like macros
Object-like macros replace every occurrence of defined *identifier* with *replacement-list*.

### Function-like macros
Function-like macros replace each occurrence of defined *identifier* with *replacement-list*, additionally taking a number of arguments, which then replace corresponding occurrences of any of the *parameters* in the *replacement-list*.
The syntax of a function-like macro invocation is similar to the syntax of a function call: each instance of the macro name followed by a ( as the next preprocessing token introduces the sequence of tokens that is replaced by the *replacement-list*. The sequence is terminated by the matching ) token, skipping intervening matched pairs of left and right parentheses.

### # and ## operators
In function-like macros, a # operator before an identifier in the replacement-list runs the identifier through parameter replacement and encloses the result in quotes, effectively creating a string literal. In addition, the preprocessor adds backslashes to escape the quotes surrounding embedded string literals, if any, and doubles the backslashes within the string as necessary. All leading and trailing whitespace is removed, and any sequence of whitespace in the middle of the text (but not inside embedded string literals) is collapsed to a single space. This operation is called "stringification". If the result of stringification is not a valid string literal, the behavior is undefined.

A ## operator between any two successive identifiers in the replacement-list runs parameter replacement on the two identifiers (which are not macro-expanded first) and then concatenates the result. This operation is called "concatenation" or "token pasting". Only tokens that form a valid token together may be pasted: identifiers that form a longer identifier, digits that form a number, or operators + and = that form a +=. A comment cannot be created by pasting / and * because comments are removed from text before macro substitution is considered. If the result of concatenation is not a valid token, the behavior is undefined.

### #undef directive
The #undef directive undefines the *identifier*, that is cancels previous definition of the identifier by #define directive. If the identifier does not have associated macro, the directive is ignored.

## Syntax
A macro definition is independent of block structure, and is in effect from the `#define` directive that defines it until either a corresponding `#undef` directive or the end of the compilation unit is encountered.

The `#define` directive has the following syntax:
```cpp
#define identifier replacement-list newline
#define identifier ( identifier-list(opt) )
replacement-list newline
```
If the replacement-list is empty, subsequent occurrences of the identifier are deleted from the source file.

The first form of the `#define` directive is called an object-like macro. The second form is called a function-like macro.

The `#undef` directive has the following syntax:
```cpp
#undef identifier newline
```
This directive cancels a previous definition of the identifier by #define . Redefining a macro previously defined is not legal, unless the new definition is precisely the same as the old.

### semicolon swallowing
```cpp
#define FOO(X) do { f(X); g(X); } while (0)
#define FOO(X) if (1) { f(X); g(X); } else
```
## An example of #define #include #undef


Here's a scenario to illustrate...

Lets say that timer.h provides a macro tick_count() that returns the number of timer interrupts that occured.

One module (rpm_reader.h) using timer A for interval timing:
```cpp
#define _TIMERA   
#include "timer.h"   
#undef _TIMERA 
```
In another module (lap_time.h) is using timer C for its interval timing
```cpp
#define _TIMERC  
#include "timer.h"  
#undef _TIMERC  
```
The rpm_reader would return the tick count from timer A when it called tick_count() and lap_time would get its count from timer C.

## Code Example
```cpp
#include <iostream>
 
// Make function factory and use it
#define FUNCTION(name, a) int fun_##name() { return a;}
 
FUNCTION(abcd, 12)
FUNCTION(fff, 2)
FUNCTION(qqq, 23)
 
#undef FUNCTION
#define FUNCTION 34
#define OUTPUT(a) std::cout << "output: " #a << '\n'
 
// Using a macro in the definition of a later macro
#define WORD "Hello "
#define OUTER(...) WORD #__VA_ARGS__
 
int main()
{
    std::cout << "abcd: " << fun_abcd() << '\n';
    std::cout << "fff: " << fun_fff() << '\n';
    std::cout << "qqq: " << fun_qqq() << '\n';
 
    std::cout << FUNCTION << '\n';
    OUTPUT(million);               //note the lack of quotes
 
    std::cout << OUTER(World) << '\n';
    std::cout << OUTER(WORD World) << '\n';
}
```
p.s.: An identifier **__VA_ARGS__** that occurs in the replacement list shall be treated as if it were a parameter, and the variable arguments shall form the preprocessing tokens used to replace it.

output:
>abcd: 12
>
>fff: 2
>
>qqq: 23
>
>34
>
>output: million
>
>Hello World
>
>Hello WORD World
>
>Program ended with exit code: 0
>
