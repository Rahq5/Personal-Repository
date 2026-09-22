
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

#### Numbers part

Looking at this example:

```rust
let x = 4;
let y = x;

// both are i32 by default
```

Because both are `i32` — a 32-bit signed number with a fixed, small, predictable size — they're stored on the stack. When `let y = x` runs, the value `4` is simply copied, and a new copy is pushed onto the stack for `y`. Now the stack holds:

```
Stack: {
    [x = 4]
    [y = 4]
}
```

---

#### String part

Here everything is different. A `String`'s data is unpredictable and dynamically sized, so it can't live on the stack — it has to be saved on the **heap** instead.

A `String` is made of 3 parts:

- **pointer:** saved on the stack, points to where the actual string data lives in the heap
- **length:** how many bytes of that allocation are currently in use
- **capacity:** the total bytes the `String` has been given by the operating system

> **Question:** what happens if the length somehow exceeds the capacity? Length is dynamic (grows/shrinks as you add or delete data), while capacity stays static (a fixed ceiling on total bytes available) — so length can never legally exceed capacity. If more space is needed than capacity allows, Rust reallocates a bigger block on the heap behind the scenes and updates the pointer and capacity to match.

Here's how both parts are stored — the metadata on the stack, the actual bytes on the heap:

```
s1 (stack)                          heap
┌──────────┬───────┐                ┌───────┬───────┐
│ name     │ value │                │ index │ value │
├──────────┼───────┤                ├───────┼───────┤
│ ptr      │   ●───┼───────────────▶│   0   │   h   │
│ len      │   5   │                │   1   │   e   │
│ capacity │   5   │                │   2   │   l   │
└──────────┴───────┘                │   3   │   l   │
                                     │   4   │   o   │
                                     └───────┴───────┘
```

---

##### The double free problem

> **Double free:** a bug where the same block of heap memory gets freed (deallocated) twice. This corrupts memory management and can crash the program or, worse, be exploited as a security vulnerability.

This happens in languages without ownership tracking (like C) when two separate variables/pointers both think they "own" the same heap allocation, and both try to clean it up when they go out of scope.

**How it would happen if Rust allowed copying `String` freely** — i.e. if `let s2 = s1;` copied the stack part (ptr, len, capacity) _without_ invalidating `s1`, the same way it does for integers:

```
1. s1 and s2 both point to the SAME heap block
2. s2 goes out of scope → heap memory freed
3. s1 goes out of scope → tries to free the SAME memory again
                            ↳ DOUBLE FREE — undefined behavior
```

This could crash the program, corrupt unrelated data allocated into that now-freed slot, or in the worst case be exploited by an attacker — a classic memory-safety vulnerability class in C/C++.

---

##### How Rust actually fixes it: move semantics

Instead of allowing two owners, Rust **invalidates `s1`** the moment `s2` takes over the pointer. This is called a **move**.

```rust
let s1 = String::from("hello");
let s2 = s1;   // s1 is MOVED into s2
```

```
s1 (stack)                          heap
┌──────────┬───────┐                ┌───────┬───────┐
│   ✗ INVALID ✗    │                │ index │ value │
└──────────┴───────┘                ├───────┼───────┤
                                     │   0   │   h   │
s2 (stack)                          │   1   │   e   │
┌──────────┬───────┐                │   2   │   l   │
│ ptr      │   ●───┼───────────────▶│   3   │   l   │
│ len      │   5   │                │   4   │   o   │
│ capacity │   5   │                └───────┴───────┘
└──────────┴───────┘
```

Now there's only **one** owner (`s2`). When it goes out of scope, Rust frees the heap memory exactly **once** — no double free is possible.

Trying to use `s1` after the move produces a **compile-time error**, not a runtime crash:

```rust
let s1 = String::from("hello");
let s2 = s1;

println!("{s1}, world!"); // ❌ error[E0382]: borrow of moved value: `s1`
```

```
error[E0382]: borrow of moved value: `s1`
 --> src/main.rs:4:20
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 |     println!("{s1}, world!");
  |                ^^ value borrowed here after move
```

---

##### If you actually want two independent copies: `.clone()`

If the intent is a genuine, separate copy of the heap data (not just the pointer), Rust requires making that explicit with `.clone()`:

```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {s1}, s2 = {s2}"); // ✅ both valid
```

```
s1 (stack)                          heap (two separate blocks)
┌──────────┬───────┐                ┌───────┬───────┐
│ ptr      │   ●───┼───────────────▶│ h e l l o     │ ← s1's own data
└──────────┴───────┘                └───────┴───────┘

s2 (stack)                          ┌───────┬───────┐
┌──────────┬───────┐                │ h e l l o     │ ← s2's own copy
│ ptr      │   ●───┼───────────────▶└───────┴───────┘
└──────────┴───────┘
```

Now each variable owns its **own** heap allocation, and each is freed independently when it goes out of scope — safely, with no shared ownership.

---

| Scenario                                      | What happens                    | Result                              |
| --------------------------------------------- | ------------------------------- | ----------------------------------- |
| Two pointers, one heap block, both auto-freed | Double free                     | 💥 Undefined behavior (C-style bug) |
| Rust's move                                   | Old variable invalidated        | ✅ Only one owner, one free          |
| `.clone()`                                    | New independent heap allocation | ✅ Two owners, two separate frees    |


### 02-Data and variables interaction: Clone
in other languages there's two kinds of copying data:
1. Shallow copy: to make two pointers to the same heap data
2. Deep copy: to copy the whole deep data and paste it to a new one.

unfortunately in rust we cant do shallow copying cuz as we said before this will make double-free errors.
So if i wanted badly to get two copies of the same data without losing any variable (or get the invalidated) i would use `.clone()`


#### Deep-copying heap data with `.clone()`

**Definition:** `.clone()` is a method that explicitly duplicates the **heap data** of a `String`, not just its stack metadata (ptr/len/capacity). Seeing `.clone()` in code is a visual signal that something potentially expensive is happening — arbitrary code is running to copy the underlying data.

```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {s1}, s2 = {s2}");
```

**Output:**

```
s1 = hello, s2 = hello
```

Both `s1` and `s2` are independently valid — each owns its own separate heap allocation.

---

#### Stack-Only Data: `Copy`

**Definition:** for types that live entirely on the stack (like integers), copying is cheap — a simple bitwise duplication. There's no meaningful difference between a "shallow" and "deep" copy here, since there's no heap data involved at all. Because of this, Rust lets these types be duplicated automatically on assignment, without needing `.clone()`.

```rust
let x = 5;
let y = x;

println!("x = {x}, y = {y}");
```

**Output:**

```
x = 5, y = 5
```

Notice: no `.clone()` call, yet `x` is still valid after `y` is created — unlike `String`, this is _not_ a move.

> **`Copy` trait:** a special Rust annotation placed on stack-only types. If a type implements `Copy`, assigning it to another variable performs a trivial copy instead of a move — the original variable stays valid.

**Restriction:** a type **cannot** implement `Copy` if it (or any of its parts) implements the `Drop` trait (code that runs custom cleanup when a value goes out of scope). Attempting this combination is a compile-time error.

**Types that implement `Copy`:**

- All integer types (e.g. `u32`)
- `bool`
- All floating-point types (e.g. `f64`)
- `char`
- Tuples, _only if every element inside is also `Copy`_ — `(i32, i32)` ✅, but `(i32, String)` ❌ (because `String` isn't `Copy`)

**General rule:** simple scalar values (and groups of them) can be `Copy`. Anything requiring heap allocation or representing a resource cannot.

---

#### Conclusion — Two Categories of Data
| | **Fixed-size, stack-only** | **Dynamically-sized, heap-backed** |
|---|---|---|
| **Examples** | `i32`, `f64`, `bool`, `char`, tuples/arrays of `Copy` types | `String`, `Vec` |
| **Size known at compile time?** | ✅ Yes | ❌ No — can change at runtime |
| **Storage** | Entirely on the stack | Stack holds ptr/len/capacity; actual data on heap |
| **Assignment behavior** | Cheap bitwise copy (`Copy` trait) | **Move** by default (prevents double free) |
| **Getting a true duplicate** | Automatic — just assign | Must call `.clone()` explicitly |
**Key nuance:** fixed size alone isn't _sufficient_ for `Copy` — the type must also **not** implement `Drop`, and every field/element inside it must itself be `Copy`.



### 03-Ownership and functions 

These notes cover how ownership behaves once function calls enter the picture, and a related mutability distinction between `&str` and `String`. Together they explain why passing values around in Rust feels stricter than in garbage-collected languages, and why `mut` doesn't mean the same thing for every type.

What's covered:

- Ownership Through Function Calls (moves across scope boundaries, returning ownership, the tuple-passing workaround)
- `&str` vs `String` under `mut` (pointer reassignment vs. actual heap mutation)

---

#### Ownership Through Function Calls

Passing a value into a function works exactly like a `let` assignment: it either moves or copies, depending on the type. When a `String` named `s` is passed into `takes_ownership(s)`, a new variable binding is created — the function's parameter (`some_string`) — in a completely separate scope. Ownership relocates from `s` in `main` to `some_string` inside the function. `s` becomes invalid; using it afterward is a compile-time error.

> **Move across a function boundary:** the same move mechanism as `let s2 = s1;`, just applied when a value crosses into a function's scope instead of into a sibling variable.

```rust
fn main() {
    let s = String::from("hello");
    takes_ownership(s);
    // s is no longer valid here
}

fn takes_ownership(some_string: String) {
    println!("{some_string}");
} // some_string goes out of scope and is dropped
```

**Why this is stricter than Java:** Java has no ownership system — objects stay alive as long as any reference points to them, and garbage collection cleans them up later, at runtime, whenever it decides nothing points to them anymore. Rust has no GC, so instead it enforces, **at compile time**, that there's always exactly one owner for a piece of heap data — preventing two variables from both trying to free the same memory (a double-free).

**Getting ownership back out:** because the function parameter is a distinct variable in a distinct scope, there's no way back to the caller's scope except through `return`. When a function returns a value, ownership moves out of the function and into whatever variable captures it at the call site.

```rust
fn main() {
    let s1 = gives_ownership();          // moves into s1
    let s2 = String::from("hello");
    let s3 = takes_and_gives_back(s2);   // s2 moves in, return moves into s3
}

fn gives_ownership() -> String {
    let some_string = String::from("yours");
    some_string
}

fn takes_and_gives_back(a_string: String) -> String {
    a_string
}
```

Chain two functions like this and you get a clean sequence of ownership relocations — one move per step, no copying involved.

**The tuple workaround (and its cost):** if a function needs to use a value _and_ hand back both that value and some newly computed result, Rust lets you bundle both into a tuple:

```rust
fn calculate_length(s: String) -> (String, usize) {
    let length = s.len();
    (s, length)
}
```

This works, but it's ceremony-heavy — every value you still need afterward has to be manually passed back out, alongside anything else the function produces.

> **No runtime ownership query:** there's no way to ask "who owns this?" at runtime the way you might check a reference count. Ownership is a purely static, compile-time property. The current owner is whichever variable most recently received the value and hasn't since moved it elsewhere; try to use a variable after it's moved, and the compiler stops you with a "value moved here" error. (Exception: `Rc<T>`/`Arc<T>`, covered later, intentionally support multiple owners and expose a real `strong_count()`.)

This tuple-passing tedium is exactly the gap **references (`&`)** are built to close — letting a function borrow a value temporarily without taking ownership at all, so nothing needs to be manually returned just to stay usable.

---

#### `&str` vs `String` under `mut`

> **Key distinction:** with `&str`, `mut` only lets you change _which_ hardcoded binary string you're pointing to — the data itself is read-only. With `String`, `mut` lets you change the actual text data sitting in heap memory.

**1. Changing the pointer (`&str`)**

```rust
fn main() {
    let mut str: &str = "Hello"; // pointing to "Hello" in the binary
    str = "hi";                  // now pointing to a different binary string "hi"
    // Both "Hello" and "hi" remain unchanged in the binary — only the pointer moved.
}
```

**2. Changing the actual data (`String`)**

```rust
fn main() {
    let mut s = String::from("Hello"); // allocates "Hello" on the heap
    s.push_str(" World");              // modifies the actual data on the heap
    // The heap memory now dynamically holds "Hello World".
}
```

The difference comes down to where each type's data lives: `&str` mutability just re-points a stack reference at different, already-existing, read-only binary data. `String` mutability actually rewrites bytes in a heap allocation `String` owns.
