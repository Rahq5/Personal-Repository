# Introduction

## What is C?

C is a **general-purpose, compiled, low-level programming language** that gives the programmer direct control over memory and hardware. It produces fast, efficient machine code with minimal abstraction between your code and what the CPU actually executes. Almost every operating system kernel, embedded system, and system utility you use today is built on C.

---

## Creator, Year & Reason

| Creator          | Dennis Ritchie                                                                    |
| ---------------- | --------------------------------------------------------------------------------- |
| Year             | 1972                                                                              |
| **Organization** | Bell Labs (AT&T)                                                                  |
| Reason           | Created to rewrite the **Unix operating system, previously written in Assemblem** |


> Unix and C were born together. C _was_ made for OS development — this is exactly why it's the right tool for studying OS and Linux internals.

---

## Advantages

- **Extremely fast** — compiles directly to machine code, minimal overhead
- **Direct memory control** — manual allocation with `malloc()` / `free()`, pointer arithmetic
- **Portable** — same C code compiles on Linux, macOS, Windows, embedded chips
- **Close to the OS** — system calls (`fork`, `read`, `write`, `socket`) are C functions
- **Massive ecosystem** — decades of libraries, tools, and documentation
- **Foundation for everything** — Python, Java, Ruby interpreters are written in C
- **Small runtime** — no garbage collector, no virtual machine, no hidden processes

---

## Disadvantages

- **Manual memory management** — you _must_ free what you allocate; forgetting causes **memory leaks** (program slowly consumes RAM until it crashes)
- **No bounds checking** — writing past an array's end = **buffer overflow** (a major class of security vulnerabilities)
- **No built-in OOP** — no classes, no inheritance, no polymorphism
- **Verbose for complex tasks** — things like string handling require extra care
- **Undefined behavior** — many mistakes (null pointer dereference, uninitialized variables) don't crash immediately but cause unpredictable bugs
- **No standard package manager** — dependency management is manual compared to modern languages

---

## Characteristics

|Characteristic|What it means|
|---|---|
|**Compiled**|Code is translated fully to machine code before running (unlike Python which interprets line by line)|
|**Statically typed**|Every variable must have a declared type (`int`, `char`, `float`) at compile time|
|**Procedural**|Code is organized into functions, not objects or classes|
|**Imperative**|You write _how_ to do something step by step, not _what_ you want (like SQL does)|
|**Weakly typed**|Types can be implicitly converted, e.g. `int` → `char`, which can cause silent bugs|
|**Portable**|Write once, compile anywhere — as long as a C compiler exists for that platform|
|**Minimal standard library**|Unlike Java or Python, C's built-in library is small — most power comes from OS-level calls|
|**Pointer-based**|Memory addresses are first-class values you can store, pass, and manipulate directly|

---

## Quick Mental Model

```
Your C Code
    ↓  (gcc compiler)
Machine Code (binary)
    ↓
CPU executes it directly
    ↑
No VM. No interpreter. No runtime manager.
You and the metal.
```

---

## Official Resources

- 📘 Standard reference book: _The C Programming Language_ — Kernighan & Ritchie (K&R)
- 🌐 GNU C Manual: https://www.gnu.org/software/gnu-c-manual/
- 🌐 C standard (ISO/IEC 9899): https://www.open-std.org/jtc1/sc22/wg14/
- 🌐 Linux man pages (system calls in C): https://man7.org/linux/man-pages/

---

_Next: C memory model → pointers → system calls_

# Hello World (explained)
## C — Hello World Breakdown
```C
#include <stdio.h>
int main(void){
	printf("hello rawi");
	return 0;
}
```
### Line by Line

**`#include <stdio.h>`**
- `#include` is a **preprocessor directive** — it runs *before* compilation
- It tells the compiler: "paste the contents of `stdio.h` into my file"
- `stdio.h` = **Standard Input/Output header** — gives you access to `printf`, `scanf`, etc.
- Without it, the compiler has no idea what `printf` is

**`int main(void)`**
- Every C program **must** have a `main()` function — it's the entry point (where execution starts)
- `int` = this function will return an integer when it finishes
- `void` = takes no arguments/parameters
- The OS itself calls `main()` when you run your program

**`return 0;`**
- Sends the value `0` back to the OS when the program finishes
- `0` = success, anything else = something went wrong
- The OS can read this value — shell scripts use it to check if a program succeeded

## compiling and running
in c compiling and running is different and kinda harder
so using `HelloWorld.c` file for our example
command:
`gcc Helloworld.c -o test && ./test`
1. compile file using gcc `gcc Helloworld.c`
2. write result into output file `-o test`
3. view output `./test`
>Note: the `&&` in the example is a smart move from me to compile and output in one command, or you gonna seperate them by commanding `./<outputFile>`

## Header Files `.h`
A header file is a **`.h` file** that contains **declarations** — it tells the compiler "this function exists and here's its signature" without containing the actual implementation. Think of it as a **menu** — it lists what's available, the actual kitchen (implementation) is elsewhere in compiled libraries.

When you write `#include <stdio.h>`, the preprocessor literally **copy-pastes** that file's contents into your code before compilation starts.

- stddef.h - Defines several useful types and macros.
- stdint.h - Defines exact width integer types.
- stdio.h - Defines core input and output functions
- stdlib.h - Defines numeric conversion functions, pseudo-random number generator, and memory allocation
- string.h - Defines string handling functions
- math.h - Defines common mathematical functions.