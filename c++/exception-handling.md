Exception handling allows independently developed parts of a program to communicate about and handle problems that arise at run time. Exceptions let us separate problem detection from problem resolution. One part of the program can detect a problem and can pass the job of resolving that problem to another part of the program. The detecting part need not know anything about the handling part, and vice versa.

In C++, exception handling involves

- throw expressions, which the detecting part uses to indicate that it encountered something it can’t handle. We say that a throw raises an exception.

- try blocks, which the handling part uses to deal with an exception. A try block starts with the keyword try and ends with one or more catch clauses. Exceptions thrown from code executed inside a try block are usually handled by one of the catch clauses. Because they “handle” the exception, catch clauses are also known as exception handlers.

- A set of exception classes that are used to pass information about what happened between a throw and an associated catch.

## `throw()` and `noexcept`

The detecting part of a program uses a throw expression to raise an exception. A throw consists of the keyword throw followed by an expression.
```cpp
// first check that the data are for the same item
if (item1.isbn() != item2.isbn())
    throw runtime_error("Data must refer to same ISBN");
// if we're still here, the ISBNs are the same
cout << item1 + item2 << endl;
```
In this code, if the ISBNs differ, we throw an expression that is an object of type `runtime_error`. Throwing an exception terminates the current function and transfers control to a handler that will know how to handle this error.

The keyword `noexcept` following the function parameter list indicates that the function won’t throw.

Non-throwing functions are very important for writing **exception safe code**. For instance, the usual way of writing an exception safe operator= is via a non-throwing swap() function.

Also, specifying that a function won’t throw effectively promises the callers of the nonthrowing function that they will never need to deal with exceptions. Either the function won’t throw, or the whole program will terminate; the caller escapes responsibility either way.

## Exception safe code
Programs that properly “clean up” during exception handling are said to be *exception safe*.

At the point where the exception occurs, some of the computations that the caller requested may have been done, while others remain undone. In general, bypassing part of the program might mean that an object is left in an invalid or incomplete state, or that a resource is not freed, and so on.

The fact that control passes from one location to another when a catch block is executed has two important implications:

• Functions along the call chain may be prematurely exited.

• When a handler is entered, objects created along the call chain will have been destroyed.

Because the statements following a throw are not executed, a throw is like a return: It is usually part of a conditional statement or is the last (or only) statement in a function.

## `try` and `catch` block
```cpp
try {
    program-statements
} catch (exception-declaration) {
    handler-statements
} catch (exception-declaration) {
    handler-statements
} // . . .
```
Following the try block is a list of one or more catch clauses. A catch consists of three parts: the keyword `catch`, the declaration of a (possibly unnamed) object within parentheses (referred to as an exception declaration), and a block. When a catch is selected to handle an exception, the associated block is executed. Once the catch finishes, execution continues with the statement **immediately following the last catch clause of the try block**.

The type of the thrown expression, together with the current call chain, determines which handler will deal with the exception.

In complicated systems, the execution path of a program may pass through multiple try blocks before encountering code that throws an exception. For example, a try block might call a function that contains a try, which calls another function with its own try, and so on.

The search for a handler reverses the call chain. When an exception is thrown, the function that threw the exception is searched first. If no matching catch is found, that function terminates. The function that called the one that threw is searched next. If no handler is found, that function also exits. That function’s caller is searched next, and so on back up the execution path until a catch of an appropriate type is found.

If no appropriate catch is found, execution is transferred to a library function named **terminate**. The behavior of that function is system dependent but is guaranteed to stop further execution of the program.

This process, known as **stack unwinding**, continues up the chain of nested function calls until a catch clause for the exception is found, or the main function itself is exited without having found a matching catch.

- Objects Are Automatically Destroyed during Stack Unwinding



## Standard exceptions

![image](https://user-images.githubusercontent.com/78017591/121018987-96618d80-c7d1-11eb-8eac-0c22f558cb34.jpeg)

We can only default initialize `exception`, `bad_alloc`, and `bad_cast` objects; it is not possible to provide an initializer for objects of these exception types.

The other exception types have the opposite behavior: We can initialize those objects from either a `string` or a C-style string, but we cannot default initialize them. When we create objects of any of these other exception types, we must supply an initializer. That initializer is used to provide additional information about the error that occurred.

### `what()` member function
Each of the library exception classes defines a member function named `what`, and it's the only defined operation. These functions take no arguments and return a C-style character string (i.e., a const char*). The `what()` member of runtime_error returns a copy of the string used to initialize the particular object.

Because `what` is **virtual**, if we catch a reference to the base-type, a call to the `what` function will execute the version appropriate to the dynamic type of the exception object.

### Inheritance 
```cpp
// hypothetical exception classes for a bookstore application
class out_of_stock: public std::runtime_error {
public:
    explicit out_of_stock(const std::string &s):
                       std::runtime_error(s) { }
};
class isbn_mismatch: public std::logic_error {
public:
    explicit isbn_mismatch(const std::string &s):
                          std::logic_error(s) { }
    isbn_mismatch(const std::string &s,
        const std::string &lhs, const std::string &rhs):
        std::logic_error(s), left(lhs), right(rhs) { }
    const std::string left, right;
};
```
Use:
```cpp
// throws an exception if both objects do not refer to the same book
Sales_data&
Sales_data::operator+=(const Sales_data& rhs)
{
    if (isbn() != rhs.isbn())
        throw isbn_mismatch("wrong isbns", isbn(), rhs.isbn());
    units_sold += rhs.units_sold;
    revenue += rhs.revenue;
    return *this;
}
```
It's the best practice to always catch own exception types by reference
```cpp
// use the hypothetical bookstore exceptions
Sales_data item1, item2, sum;
while (cin >> item1 >> item2) {  // read two transactions
    try {
        sum = item1 + item2;     //  calculate their sum
        // use sum
    } catch (const isbn_mismatch &e) {
      cerr << e.what() << ": left isbn(" << e.left
           << ") right isbn(" << e.right << ")" << endl;
    }
}
```
