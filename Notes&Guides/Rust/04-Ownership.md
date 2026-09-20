
# 01-Introduction
_Ownership_ is a set of rules that govern how a Rust program manages memory. All programs have to manage the way they use a computer’s memory while running.Rust uses an approach: Memory is managed through a system of ownership with a set of rules that the compiler checks. If any of the rules are violated, the program won’t compile.

# 02-The Stack and Heap 
**Why it matters:** in a systems language like Rust, whether data lives on the stack or the heap affects how the language behaves, and it's the foundation for understanding _ownership_ later in this chapter.

### The Stack

> **LIFO (last in, first out):** data is added and removed in reverse order — like a stack of plates, you only add/remove from the top.

- Adding data = **pushing**
- Removing data = **popping**
- All stack data must have a **known, fixed size** at compile time.
- Fast, because there's no searching — new data always goes on top.

---

### The Heap

> **Allocating:** requesting a chunk of heap memory. The allocator finds a big-enough empty spot, marks it used, and returns a **pointer** (the address of that spot).

- Less organized than the stack — data size can be unknown at compile time or can change.
- The **pointer itself** is fixed-size, so it can be stored on the stack — but to get the actual data, you follow the pointer to the heap.
- Slower to allocate (the allocator has to search + do bookkeeping) and slower to access (following a pointer means jumping around in memory, which is less efficient for the processor).

---

### Function Calls & the Stack

When a function is called:

- Its arguments and local variables (including any heap pointers) get **pushed onto the stack**.
- When the function ends, those values get **popped off**.

---

### Why Ownership Exists

Ownership exists to solve heap-data problems:

- Tracking what code is using which heap data
- Minimizing duplicate data on the heap
- Cleaning up unused heap data so memory doesn't run out

Once ownership "clicks," you rarely need to think about stack/heap explicitly day-to-day — but knowing ownership's core purpose is _managing heap data_ explains _why_ it works the way it does.


# 03-Ownership_Rules
First, let’s take a look at the ownership rules. Keep these rules in mind as we work through the examples that illustrate them:

- Each value in Rust has an _owner_.
- There can only be one owner at a time.
- When the owner goes out of scope, the value will be dropped.


## 01-Variable scope
comparing to garbage collector in other lanuages , it searches for values that dont have pointers , then frees there memory but unlike ownership were
the main idea here is when a variable holds a value ,it actually owns it, so when this variable is out of scope then the value is dropped like this example
```rust
 { // variable isnt valid
  
 let s = "hello" ; // here the variable is valid 
 
 // some more logic while the variable is valid 
 } // out of scope so the variable isnt valid anymore and value is dropped or freed 
```

look at this code also vv:
```rust

let s = String::from("Hello"); // heap-allocated
let x = "Hello"; // String literal
```

there's actually a difference in memory behavior that needs to be kept in mind 
- **Heap-allocated:** this one once the owner goes invalid, will be freed from memory
- **String literal:** this one is hard coded into binaries, stays as long as the program is running

### 01-Data and variables interaction: move

#### numbers part
looking at this example:
```rust
let x = 4;
let y = x;

//both are i32 by default
```
because both are i32 which means a (32 bit signed) number which is a fixed small predictable data so it will be stored in the stack, and for variables like `y` they just bind the value 4 to y and the new copy of `y` gets pushed to the stack. so now the stack has these data:

```
Stack:{
[x = 4]
[y = 4]
}
```

#### String part
here everything is different, cuz the String nature are unpredictable, dynamic-sized, that means we cant store them in the stack so another solution is to save them into the "heap".

using `String` we make the string get saved to heap, `String` has 3 parts:
- **pointer:** pointer saved in stack to point to where does the string saved in the heap
- **length:** is how much memory in bytes showing the size of string currently using 
- **capacity:** is the total amount of memory, in bytes, that the String has received from the operating system

