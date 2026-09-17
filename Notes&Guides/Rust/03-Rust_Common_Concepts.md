
# 01-Variables and mutability
## 01-Mutability

rust have a concept called **Mutability** which is the type of variables that wont change their values by overwriting.
looking to this code snippet:
```rust
let x = 5;
println!("{x}");

x = 6 ;
println!("{x}");
```
this code will make an error which are :
```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("The value of x is: {x}");
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
  |
help: consider making this binding mutable
  |
2 |     let mut x = 5;
  |         +++

For more information about this error, try `rustc --explain E0384`.
error: could not compile `variables` (bin "variables") due to 1 previous error

```

which means rust cant re-assign a immutable variable.
the thing is the variables in rust are immutable by default so without adding the expression `mut` this will make it still immutable.
now i will add `mut` and fix the problem:
```rust
let mut x = 5;
println!("{x}");

x = 6 ;
println!("{x}");
```
result:
```rust
5
6
```

## 02-Declaring constants
**constant** is an unchangeable value bound to a name that is evaluated at **compile time** and remains valid for the entire duration of the program.

declaring constant simply goes like this:
```rust
const SOME_CONSTANT_VAR: u32= "some value here";
```

and that's it. 

actually there was a question about using mutability and constant.

- **If immutable `let` already can't change, why use `const` at all?**
	- **Scope** — `const` can live outside any function (global); `let` can't exist outside a function body at all.
	- **When it's computed** — `const` is baked to binary at compile time (before the program runs); `let` can hold a value only known at runtime.
	- **Memory cost** — `const` gets inlined as a literal, no memory slot; `let` still takes a real memory location, just never rewritten.
	- **Intent** — `const` says "fixed value the whole program should know"; `let` says "data for this function's current logic."

## 03-Shadowing
**Shadowing:** declaring a new variable with `let`, using the same name as an existing one. The new binding takes over that name for the rest of its scope — it doesn't modify the old value, it creates a separate one that hides the old one from view.

```rust
fn main() {
    let x = 5;          // x #1: bound to 5

    let x = x + 1;       // x #2: new binding, reads old x (5), adds 1 → 6
                          // x #1 (5) is now shadowed, hidden by name

    {
        let x = x * 2;    // x #3: new binding, reads x #2 (6), multiplies by 2 → 12
                           // only exists inside this inner scope
        println!("The value of x in the inner scope is: {x}"); // prints 12
    } // inner scope ends here — x #3 goes out of scope

    println!("The value of x is: {x}"); // x #2 (6) is visible again, prints 6
}
```

in debugging the previous code you will see three x's written like this:
```
x = 5
x #2 = 6
x #3 = 12
```

but the result will be 
```
The value of x in the inner scope is: 12
The value of x is: 6
```

the question here:
- **why use shadowing and how it's different from `mut`?**:
	- **Shadowing** 
		  reusing `let` with the same variable name to create a **brand new binding**; the old one is hidden (shadowed), not modified.
		  
	- **Scope-limited** 
		  a shadowed value inside `{ }` reverts back once that inner scope ends (like the `x * 2` example: inner scope sees `12`, outer still sees `6`).
		  
	- **Stays immutable after transforming** 
		  you can do `let x = x + 1;` to transform a value step by step, but each result is still immutable — no `mut` needed.
		  
	- **Can change type**
		  since it's a _new_ variable each time, shadowing lets you reuse a name with a different type (`String` → `usize`), which `mut` cannot do (type is fixed once declared).
		  
	- **Compile-time safety** 
		  accidentally reassigning without `let` (i.e. forgetting the keyword) throws a compile error, unlike a silent bug.

# 02-Datatypes
