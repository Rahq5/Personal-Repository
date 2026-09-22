# 01-Introduction

in the last point in [04-Ownership > Ownership Through Function Calls](04-Ownership#Ownership Through Function Calls) we learnt that the only way to transfer ownership from variable to another without using references is using tideous (boring) functions so here comes the borrowing to solve this problem

- Borrowing: is creating a reference to a value so a function can use this value with out taking the ownership of it, then causing the problem i wrote in the beginning

and there are two types of references:

- immutable references: which only READS the value without changing anything in it - can be multiple to one value
- mutable references: which can apply changes to the original value - only one reference

Question: why would use immutable refs since am not able to change anything in it?: the answer is you can apply function that reads it only, like for a String cases you can get length, compare it to other values, iterating over it and so on.

**Example — passing a reference instead of ownership:**

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1); // here you passed with the "&"

    println!("The length of '{s1}' is {len}.");
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

`&s1` creates a reference to `s1`'s value without taking ownership of it — no tuple return needed, and `s1` stays valid after the call. Because `s` never owned the `String`, nothing gets dropped when `s` goes out of scope at the end of `calculate_length`.

> **Reference:** an address you can follow to reach data owned by some other variable — similar to a pointer, but guaranteed to always point to a valid value of a specific type for as long as the reference exists.

> **Dereferencing** (the opposite of referencing) uses the `*` operator — covered in more depth later.

**Immutable references are read-only.** Trying to mutate through one fails to compile:

```rust
fn main() {
    let s = String::from("hello");
    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world"); // ❌ error[E0596]
}
```

```
error[E0596]: cannot borrow `*some_string` as mutable, as it is behind a `&` reference
```

Just like variables, references are immutable by default — you can't modify something you've only borrowed a read-only view of.

---

# 02-Mutable References

Why does rust forbids multiple mutable pointers?: to prevent Race condtions that would occur at these cases:

1. two or more pointer try to change the same data
2. at least one of the pointers is being used to write to the data
3. no mechanism to sync access to data Note: they just removed the headache with dealing with race conditions and blocked it fromthe begainning or in arabic we say "ريحوا راسهم"

**Fixing the earlier example with a mutable reference:**

```rust
fn main() {
    let mut s = String::from("hello");
    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world"); // ✅ works now
}
```

Three things changed: `s` is declared `mut`, the call site uses `&mut s`, and the function signature takes `&mut String` — making it explicit that `change` will mutate the borrowed value.

**The one-mutable-reference-at-a-time rule:**
```rust
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s; // error cannot borrow `s` as mutable more than once at a time

println!("{r1}, {r2}");
```

This is exactly the mechanism that blocks a **data race** at compile time (the three conditions listed above) — the compiler simply refuses to compile code where it could happen, rather than letting it surface as a hard-to-track runtime bug.

**Sequential (non-overlapping) mutable references are fine**, using a scope to end the first borrow before starting the next:

```rust
let mut s = String::from("hello");

{
    let r1 = &mut s;
} // r1 goes out of scope here — safe to borrow again

let r2 = &mut s;
```

**Mixing mutable and immutable references is also restricted** — you can't have a mutable reference while immutable ones to the same value are still in use:

```rust
let mut s = String::from("hello");

let r1 = &s;      // fine
let r2 = &s;      // fine — multiple immutable refs are allowed
let r3 = &mut s;  // ❌ error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable

println!("{r1}, {r2}, and {r3}");
```

Multiple immutable references are fine together, since read-only access can never surprise another reader. A mutable reference, though, could invalidate what an immutable reader expects to see — hence the restriction.

> **A reference's scope** runs from where it's created to the **last point it's actually used** — not necessarily to the end of the block. So this compiles fine, because `r1`/`r2`'s last use (the `println!`) happens before `r3` is created:

```rust
let mut s = String::from("hello");

let r1 = &s;
let r2 = &s;
println!("{r1} and {r2}"); // r1, r2 last used here

let r3 = &mut s; // ✅ fine — r1/r2's scope already ended
println!("{r3}");
```

Borrowing errors can feel frustrating early on, but they're the compiler catching a real bug **at compile time** instead of letting it surface unpredictably at runtime — exactly where the problem is, before it ever runs.

# 03-Dangling References

**Definition:** a dangling reference is a reference that points to memory which has already been freed — the classic pointer bug in languages like C, where you can free memory while still holding a pointer to it. Rust's compiler **guarantees this can never happen**: if you have a reference to some data, the compiler ensures that data can't go out of scope before the reference to it does.

**Example — trying to create one:**

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

**Output:**

```
error[E0106]: missing lifetime specifier
 --> src/main.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
```

The error mentions _lifetimes_ (a feature covered later, in Chapter 10), but the key line explains the actual problem: the function's return type is a borrowed value with nothing valid left for it to borrow from.

**What's happening step by step:**

```rust
fn dangle() -> &String { // dangle returns a reference to a String
    let s = String::from("hello"); // s is a new String
    &s // we return a reference to the String, s
} // Here, s goes out of scope and is dropped — its memory goes away. Danger!
```

`s` is created _inside_ `dangle`. Once `dangle` finishes, `s` is deallocated — but the function tries to return a reference to it anyway. That reference would then point to invalid memory, so Rust refuses to compile it.

**The fix — return the owned value instead of a reference to it:**

```rust
fn no_dangle() -> String {
    let s = String::from("hello");
    s
}
```

This works cleanly: ownership moves out of the function to the caller, and nothing is deallocated prematurely.

---

# 04-The Rules of References

> Recap of everything covered on references:
> 
> - At any given time, you can have **either** one mutable reference **or** any number of immutable references — never both at once.
> - References must **always** be valid (no dangling references — Rust guarantees this at compile time).
