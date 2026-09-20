
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
Every value in Rust carries a data type — a label that tells the compiler what kind of data it's holding and how to work with it. Rust groups these into two subsets: **scalar** and **compound**.

One thing that trips up beginners coming from dynamically typed languages: Rust is **statically typed**, meaning every variable's type must be known at _compile time_ — before the program even runs. The compiler won't guess for you if it can't figure the type out.

Here's a case where that shows up. Trying to assign a value without a type annotation, where the compiler can't infer the type on its own:

rust

```rust
let guess = "Hello".parse().expect("enter a string plz");
// the variable is missing ": String"
```

Output:

```
error[E0284]: type annotations needed
 --> src/main.rs:3:5
  |
3 | let guess = "Hello".parse().expect("enter a string plz");
  |     ^^^^^           ----- type must be known at this point
  |
  = note: cannot satisfy `<_ as FromStr>::Err == _`
help: consider giving `guess` an explicit type
  |
3 | let guess: /* Type */ = "Hello".parse().expect("enter a string plz");
  |          ++++++++++++
```

The fix is to add an annotation:

rust

```rust
let guess: String = "Hello".parse().expect("enter a string plz");
```

**Why this happens:** `.parse()` is a _generic function_ — its return type isn't fixed. It depends on the `FromStr` trait and can produce many different types (`String`, `i32`, `f64`, etc.). Since the compiler can't guess which one you want, it needs a hint — either from the variable's type annotation (`: String`), or from **turbofish syntax** at the call site: `"Hello".parse::<String>().expect(...)`.

This isn't really about `.parse()` specifically — it's about _any_ generic call whose result type is ambiguous. `.parse()` just happens to be the most common example of it.



## 01-The scalar and compound data

There are two categories of data types in Rust:
- **Scalar:** which has a single value, rust has 4 primary types which are:
	- **Integer**
	- **float**
	- **Boolean**
	- **characters**

- **Compund** : group multiple values into one type. Rust has two primitive compound types
	- **tuples**
	- **arrays**


### Scalar

#### Integer

**Types:** Rust has signed (`i8`–`i128`, `isize`) and unsigned (`u8`–`u128`, `usize`) integers. The number is the bit-width (storage size). Signed = can be negative; unsigned = always positive (no sign needed).

**Range formula:**

- Signed `iN`: −(2^(N−1)) to 2^(N−1) − 1
- Unsigned `uN`: 0 to 2^N − 1
- Example: `i8` → −128 to 127; `u8` → 0 to 255

**`isize`/`usize`:** width depends on CPU architecture (64-bit on 64-bit systems, 32-bit on 32-bit systems). Mainly used for indexing collections.

**Literals:** Decimal (`98_222`), Hex (`0xff`), Octal (`0o77`), Binary (`0b1111_0000`), Byte (`b'A'`, u8 only). Underscore `_` is just a visual separator (`1_000` == `1000`). Type suffix allowed: `57u8`.

**Default type:** if unspecified, integers default to `i32`.

**Integer overflow:**

- Debug mode → panics (program crashes) on overflow — Rust's term for a controlled crash on error.
- Release mode (`--release`) → wraps silently via two's complement (e.g., `u8` 256 becomes 0). No crash, but the value is wrong — relying on this is considered a bug.
- Explicit handling methods: `wrapping_*` (always wraps), `checked_*` (returns `None` on overflow), `overflowing_*` (returns value + bool flag), `saturating_*` (clamps to min/max).


code example on Literals
```rust
fn main(){

let guess = 1_33;
let x = 0xff;
let y = 0o77;
let z = 0b1111_0000;

println!("{guess}");
println!("{x}");
println!("{y}");
println!("{z}");

// output:
133
255
63
240
}
```


#### Floating
**Floating-point types:** `f32` (32-bit) and `f64` (64-bit) — both signed. Default is `f64`, since on modern CPUs it's about as fast as `f32` but more precise. Follows the IEEE-754 standard (the standard format most languages use to represent decimals in binary).

```rust
let x = 2.0;       // f64 (default)
let y: f32 = 3.0;  // f32 (explicit annotation)
```

**Numeric operations:** Rust supports the usual five: addition, subtraction, multiplication, division, remainder.

- Integer division **truncates toward zero** — `-5 / 3` results in `-1`, not `-2` (it cuts off the decimal rather than rounding down).
- Remainder uses `%`, same as most languages.

```rust
let sum = 5 + 10;
let difference = 95.5 - 4.3;
let product = 4 * 30;
let quotient = 56.7 / 32.2;
let truncated = -5 / 3;   // -1
let remainder = 43 % 5;
```

#### Boolean
As in most other programming languages, a Boolean type in Rust has two possible values: `true` and `false`. Booleans are one byte in size. The Boolean type in Rust is specified using `bool`. For example:

```rust
fn main() {
	let t = true;      
	let f: bool = false; 
	// with explicit type annotation 
	}
```

The main way to use Boolean values is through conditionals, such as an `if` expression. We’ll cover how `if` expressions work in Rust in the [“Control Flow”](https://doc.rust-lang.org/book/ch03-05-control-flow.html#control-flow) section.

#### Character
**`char`:** Rust's most primitive alphabetic type. Declared with **single quotes** (`'z'`), unlike strings which use double quotes (`"z"`).

```rust
let c = 'z';
let z: char = 'ℤ';           // explicit annotation
let heart_eyed_cat = '😻';
```

**Size & representation:** `char` is always **4 bytes**, and represents a _Unicode scalar value_ — not just ASCII. This means accented letters, CJK (Chinese/Japanese/Korean) characters, emojis, and even zero-width spaces are all valid `char`s.

### Compound
#### Tuple
**Tuple:** groups multiple values of **different types** into one compound type. Fixed length — can't grow or shrink once declared.

rust

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
```

The whole tuple binds to one variable (`tup`) as a single unit.

**Destructuring:** unpack a tuple into separate variables via pattern matching.

rust

```rust
let tup = (500, 6.4, 1);
let (x, y, z) = tup;
println!("The value of y is: {y}"); // 6.4
```

**Dot-index access:** grab a single element directly with `.N` (zero-indexed, like most languages).

rust

```rust
let x: (i32, f64, u8) = (500, 6.4, 1);
let five_hundred = x.0;
let six_point_four = x.1;
let one = x.2;
```

**Unit type `()`:** the empty tuple — represents "no value." Any expression that doesn't explicitly return something implicitly returns `()`.

#### Arrays
**Array:** another way to group multiple values — unlike a tuple, **every element must be the same type**. Fixed length, same as tuples.

rust

```rust
let a = [1, 2, 3, 4, 5];
```

**Stack vs. heap:** arrays live on the **stack** (fast, fixed-size memory) rather than the heap (dynamic memory). Use an array when the element count is known and won't change — e.g., `months` (always 12).

If the collection needs to grow/shrink, use a **vector** instead (heap-allocated, covered in Chapter 8). Rule of thumb: unsure? → use a vector.

**Type annotation:**

rust

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
// [Type; Length]
```

**Same-value shorthand:**

rust

```rust
let a = [3; 5];
// same as [3, 3, 3, 3, 3]
```

**Element access:** via indexing, same as before — zero-indexed.

rust

```rust
let a = [1, 2, 3, 4, 5];
let first = a[0];   // 1
let second = a[1];  // 2
```

#### Invalid Array element access

**The scenario:** an array `[1, 2, 3, 4, 5]` (length 5) reads an index from user input (`stdin`), parses it to `usize`, then accesses `a[index]`.

rust

```rust
let index: usize = index.trim().parse().expect("Index entered was not a number");
let element = a[index];
```

**Valid input (0–4):** program prints the corresponding value normally.

**Invalid input (e.g. `10`):** program **panics** at runtime:

```
thread 'main' panicked at src/main.rs:19:19:
index out of bounds: the len is 5 but the index is 10
```

Execution stops immediately — the final `println!` never runs.

**Why this check happens at runtime, not compile time:** the compiler has no way of knowing what value the _user_ will type in ahead of time — that information only exists once the program is actually running.

**The safety principle:** every time you index (`a[index]`), Rust checks `index < length` before allowing the access. If the check fails, Rust panics rather than letting the read happen.

**Why this matters:** in many low-level languages (e.g. C), an out-of-bounds index isn't checked — it just reads whatever memory happens to be at that address, which is undefined behavior and a classic source of security bugs. Rust's runtime bounds check trades a potential crash (panic) for guaranteed memory safety — no silent garbage reads, no exploitable memory corruption.


# 03-Functions
**Basics:** declared with `fn`, followed by name and parentheses. Rust uses **snake_case** convention for function/variable names (all lowercase, underscores between words). Curly braces `{}` mark the function body.

rust

```rust
fn main() {
    println!("Hello, world!");
    another_function();
}

fn another_function() {
    println!("Another function.");
}
```

Rust doesn't care where a function is defined in the file — only that it's visible in scope to whoever calls it (order doesn't matter, unlike in some other languages).

---

### Parameters

Special variables that are part of a function's _signature_. When calling the function, you supply concrete values (technically called **arguments**, though people say "parameter" for both).

rust

```rust
fn another_function(x: i32) {
    println!("The value of x is: {x}");
}
```

**You must declare each parameter's type** in the signature — a deliberate Rust design choice, so the compiler rarely needs type hints elsewhere and can give better error messages.

Multiple parameters are comma-separated:

rust

```rust
fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {value}{unit_label}");
}
```

---

### Statements vs. Expressions

- **Statement:** performs an action, returns **no value**. Example: `let y = 6;`
- **Expression:** evaluates **to a value**. Example: `5 + 6` evaluates to `11`.

Because `let` is a statement (not an expression), you can't chain assignments like in C/Ruby:

rust

```rust
let x = (let y = 6); // ❌ doesn't compile — let statement has no value to assign
```

**Key rule: expressions don't end in a semicolon.** Adding a semicolon turns an expression into a statement, discarding its value.

A block `{}` is itself an expression and evaluates to its last line (if that line has no semicolon):

rust

```rust
let y = {
    let x = 3;
    x + 1   // no semicolon → this is the block's return value
};
// y == 4
```

---

### Functions with Return Values

Return type is declared after `->`. Return values aren't named — the function's return value is simply **the value of its final expression** (no semicolon on that line).

rust

```rust
fn five() -> i32 {
    5   // no semicolon — this is the returned expression
}

fn plus_one(x: i32) -> i32 {
    x + 1   // returns x+1
}
```

`return` keyword exists for early returns, but implicit return via the last expression is the norm.

**Common bug — accidentally adding a semicolon:**

rust

```rust
fn plus_one(x: i32) -> i32 {
    x + 1;   // ❌ now a STATEMENT, returns () instead of i32
}
```

This causes `error[E0308]: mismatched types` — the function promised `i32` but a statement returns the unit type `()`. Fix: remove the semicolon.


# 04-If_Expressions
**Basic syntax:** `if` branches code based on a condition. Code blocks tied to conditions are called **arms** (same term as `match`).
```rust
let number = 3;

if number < 5 {
    println!("condition was true");
} else {
    println!("condition was false");
}
```

`else` is optional — if omitted and the condition is false, the program just skips past the `if` block.

**Condition must be `bool` — no truthy/falsy conversion:** unlike Ruby or JavaScript, Rust never auto-converts other types to boolean.
```rust
if number {          // ❌ error: expected bool, found integer
    ...
}
```

Fix: be explicit.
```rust
if number != 0 {     // ✅ produces an actual bool
    ...
}
```

---

## `else if` — Multiple Conditions

```rust
if number % 4 == 0 {
    println!("number is divisible by 4");
} else if number % 3 == 0 {
    println!("number is divisible by 3");
} else if number % 2 == 0 {
    println!("number is divisible by 2");
} else {
    println!("number is not divisible by 4, 3, or 2");
}
```

Rust checks each condition **in order** and runs only the **first** one that's true — it doesn't check the rest, even if they'd also be true. (In the example, `6` is divisible by both `3` and `2`, but only the `%3` branch runs.)

Too many `else if` chains → considered messy; Chapter 6 introduces `match` as a cleaner alternative for this.

---

## `if` as an Expression (in a `let` statement)

Since `if` is an _expression_ (not just a control-flow statement), it can appear on the right side of `let`:

```rust
let condition = true;
let number = if condition { 5 } else { 6 };
// number == 5
```

**Type constraint:** both arms (`if` and `else`) must evaluate to the **same type**, since Rust needs to know `number`'s type at compile time — it can't leave the type undetermined until runtime.

```rust
let number = if condition { 5 } else { "six" };
// ❌ error[E0308]: `if` and `else` have incompatible types
//    (i32 vs &str)
```

# 05-Repetition_with_loops

Rust has three loop constructs: `loop`, `while`, and `for`.

### loop — runs forever until stopped

**Definition:** the `loop` keyword tells Rust to execute a block of code over and over, either forever or until explicitly told to stop (via `break`).

```rust
fn main() {
    loop {
        println!("again!");
    }
}
```

**Output:**

```
again!
again!
again!
again!
^Cagain!
```

(`^C` = manual interrupt with Ctrl+C — it runs forever otherwise.)

---

#### loop returning a value via `break`

**Definition:** you can put a value after `break` to return that value out of the loop, letting you assign the loop's result to a variable.

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;
        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {result}");
}
```

**Output:**

```
The result is 20
```

---

#### Loop labels — disambiguating nested loops

**Definition:** a label (starting with `'`) lets `break`/`continue` target an outer loop instead of only the innermost one (the default).

```rust
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}
```

**Output:**

```
count = 0
remaining = 10
remaining = 9
count = 1
remaining = 10
remaining = 9
count = 2
remaining = 10
End count = 2
```

---

### while — conditional loop

**Definition:** runs a block of code as long as a condition stays true; exits automatically once the condition becomes false.

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");
        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```

**Output:**

```
3!
2!
1!
LIFTOFF!!!
```

---

#### while for looping through a collection (not recommended — shown for comparison)

**Definition:** using `while` with a manual index counter to iterate an array — works, but error-prone (wrong bound → panic) and slower (bounds-checked every iteration).

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];
    let mut index = 0;

    while index < 5 {
        println!("the value is: {}", a[index]);
        index += 1;
    }
}
```

**Output:**

```
the value is: 10
the value is: 20
the value is: 30
the value is: 40
the value is: 50
```

---

### for — looping over a collection

**Definition:** the safe, concise way to loop over every item in a collection without managing an index manually.

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```

**Output:**

```
the value is: 10
the value is: 20
the value is: 30
the value is: 40
the value is: 50
```

---

#### for with a `Range` (countdown)

**Definition:** a `Range` (`1..4`) generates a sequence of numbers (inclusive start, exclusive end); `.rev()` reverses the sequence — commonly used for counting instead of `while`.

```rust
fn main() {
    for number in (1..4).rev() {
        println!("{number}!");
    }
    println!("LIFTOFF!!!");
}
```

**Output:**

```
3!
2!
1!
LIFTOFF!!!
```
