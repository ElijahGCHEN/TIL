The goal of this chapter is to explain the foundations of programming on Linux/Unix-based systems. This will provide a more complete picture of how a program executes on a Unix/Linux system, how to write more efficient code, and where to look when hard-to-find bugs arise.

# Linux ABI

ABI is the interface between user applications and hardware facilities. In contrast to API which is usually high abstract level and human-readable, ABI, as the name suggests (application binary interface) is in the form of machine code.

The Linux ABI, which is technically System V ABI, includes:
1. The register layout
2. The stack frame
3. Function prologs and epilogs
4. The calling convention (that is, parameter passing)
5. Exception handling
6. Virtual memory layout
7. Debugging
8. The binary object format (in this case, ELF).
9. Program loading and linking, which is discussed in the concept chapter.

## Register Layout

Including `rip` (register instruction pointer), `rsp` (register stack pointer), `rbp` (register base pointer) and other general-purpose registers.

## The Stack Frame

>The stack frame is used to store the return address of each function, and to store function parameters, return address and stack-based variables.

Each frame represents a function call, and starts with any arguments to a function beyond the first six being called (the first six arguments are passed as registers). Finally, the return address is pushed to the stack, and the function is called. Memory after the return address belongs to variables that are scoped to the function itself. This is why we call variables defined in a function *stack-based variables*. 

## Function Prologs and Epilogs

The code that manages stack frames is called the function's prolog (beginning) and epilog (ending).

## The Calling Convention

>A calling convention dictates which registers are volatile, which registers are non-volatile, which registers are used for parameter passing and in which order, and which register is used to return the result of a function.

## Exception Handling

C++ exceptions provide a way to return an error to a catch handler somewhere in the call stack.


## Executable and Linkable Format (ELF)

The Executable and Linkable Format (ELF) is the main format used in most Unix-based operating systems, including Linux. For example, a *a.out* executable file is a ELF file. ELF is a common standard file format for executable files, object code, shared libraries, and core dumps. Every ELF file begins with the hex number 0x7F, and continues with the ELF string.

>Object code is the product of a compiler.

>executable or binary, causes a computer "to perform indicated tasks according to encoded instructions"


Each ELF file is composed of sections. The sections are then grouped into segments, which are used to define which sections need to be loaded, and how (for example, some sections need to be loaded as read-write, others as read-execute, or, in some sub-optimal cases, read-write-execute).

### ELF Sections

### ELF Segments

## Unix Filesystem and processes

## The `fork()` function

`fork()` function takes the current process, and creates a duplicate child version of the process. Everything about the parent and child processes is the same, including opened file handles, memory, and so on, with the key difference being that the child process has a new process ID.

The use of fork() creates n^2 processes, with n being the total number of times fork() is called.

## The `wait()` function

## Interprocess communication (IPC)

### Unix pipes

>A pipe is a mechanism for sending information from one process to another.

### Unix shared memory

Unlike Unix pipes, Unix shared memory provides a buffer that can be read and written to by both processes. 

## The `exec()` function

The exec() system call is used to override the existing process with a completely new process.






