# 01-Introduction 
A _struct_, or _structure_, is a custom data type that lets you package together and name multiple related values that make up a meaningful group. If you’re familiar with an object-oriented language, a struct is like an object’s data attributes.

struct is similar to tuples [[03-Rust_Common_Concepts#Tuple]] but the difference that you have to name each element 

an example of declaring struct:
```rust 
struct task{
	task_name: String,
	status: bool,
	importance: i32
}
```

also these struct can be accessed and edited in this example:
```rust 
// the struct task


fn main (){

// initializing new instance of Task struc
	let task1 = Task {
		task_name: String::from("read book"),
		status: true,
		importance: 2,
	};
	
	
	println!("task name{}",task1.task_name);
	//output: read book
	
	task1.task_name = String::from("change tires");
	//this will change one of the attributes , mutable
} 

```

you saw in the code three things:
1. how to initialize a new instance of that struct
	   declare with `let` and assign your values each separated with commas
 
2. how to access a field of that instance
	   following this flow `instance_name.field_name`
	   
3. changing values of field
	   same as point 2 but the thing here that rust wont allow struct to have immutable fields, they are all mutable and you saw how i changed task name without using `mut`


# 02-Defining and instantiating structs
Structs group multiple related values together, similar to tuples — but unlike tuples, each piece of data is **named**, so you don't have to rely on order to know what a value means. This makes structs more flexible and self-documenting than tuples.

What's covered:

- Defining a struct and its fields
- Creating (instantiating) a struct
- Accessing and mutating fields with dot notation
- Returning a struct instance from a function
- The field init shorthand

---

## Defining a struct

**Definition:** a struct is declared with the `struct` keyword, a name describing what the grouped data represents, and curly brackets containing named **fields** — each with its own type. Fields can hold different types, just like tuples.

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

Think of the struct definition as a general template for the type — instances fill that template in with concrete data.

---

## Instantiating a struct

**Definition:** create an instance by naming the struct, then supplying `key: value` pairs for each field inside curly brackets. Field order in the instance doesn't have to match the order they were declared in.

```rust
fn main() {
    let user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };
}
```

---

## Accessing and mutating fields

**Definition:** dot notation (`instance.field`) reads a field's value. If the instance is declared `mut`, the same dot notation can assign a new value into a field.

```rust
fn main() {
    let mut user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };

    user1.email = String::from("anotheremail@example.com");
}
```

> **Note:** mutability applies to the **whole instance**, not individual fields — Rust doesn't let you mark only some fields as mutable while leaving others immutable.

---

## Returning a struct instance from a function

Just like any expression, a struct instance can be the last (implicit-return) expression of a function body.

```rust
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username: username,
        email: email,
        sign_in_count: 1,
    }
}
```

Repeating `username: username` and `email: email` works, but gets tedious — especially as a struct grows more fields.

---

## Field init shorthand

**Definition:** when a function parameter has the **exact same name** as a struct field, you can write just the name once instead of `field: field`.

```rust
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username, // instead of username: username
        email,
        sign_in_count: 1,
    }
}
```

This behaves identically to the previous version — Rust matches `email` and `username` to the struct's fields of the same name automatically.


# 03-Creating instances with struct update syntax

**Definition:** a shorthand for creating a new struct instance that reuses most of another instance's field values, only overriding the ones you specify. `..other_instance` fills in every remaining field from `other_instance`.

**The long way — without update syntax:**
```rust
fn main() {
    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };
}
```

**The same result, using struct update syntax:**
```rust
fn main() {
    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

`..user1` must come **last** in the struct literal — everything not explicitly set falls back to the matching field on `user1`. Fields you _do_ specify can be listed in any order, regardless of the struct's declared field order.

> **Important — this moves data, it doesn't copy it.** Struct update syntax uses `=` like a regular assignment, so it follows the same move rules covered earlier. In this example, `user1.username` (a `String`, not `Copy`) gets **moved** into `user2` — so `user1` as a whole can no longer be used afterward.

**Partial validity after the move:** if `user2` only pulled `active` and `sign_in_count` from `user1` (both `Copy` types — booleans/integers), `user1` would _still_ be valid, since `Copy` types are duplicated, not moved. And even in the example above, `user1.email` specifically is still usable — only `username`'s `String` was moved out; `email` was never touched.


# 04-Creating Different Types with Tuple Structs

**Definition:** tuple structs combine a tuple's shape with a struct's identity — they hold multiple values like a tuple (accessed by position, not name), but the `struct` keyword gives the whole group its own distinct **type name**. Useful when naming each field individually would be verbose or redundant, but you still want the tuple's type to be distinguishable from other tuples shaped the same way.

**Defining and using tuple structs:**
```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

`black` and `origin` are **different types**, even though both are made of three `i32` values — because `Color` and `Point` are separate structs. A function expecting a `Color` parameter cannot accept a `Point` argument, even though their internal shape is identical. Each struct you define is its own type, regardless of what types its fields share with another struct.

**Accessing values — same as tuples, by index:**
```rust
let red_value = black.0;
```

**Destructuring — different from tuples, requires naming the type:**
```rust
let Point(x, y, z) = origin;
```

> Unlike a plain tuple's destructuring (`let (x, y, z) = tup;`), a tuple struct's pattern must name the struct type it's destructuring — here, `Point(x, y, z)` rather than just `(x, y, z)`.


# 05-Defining Unit-like Structs
These notes cover two smaller struct topics: structs with no fields at all, and why struct fields are usually owned types (`String`) rather than references (`&str`).

## Unit-like structs

**Definition:** a struct with **no fields** at all — declared with just the `struct` keyword, a name, and a semicolon (no curly brackets, no parentheses). Called "unit-like" because it behaves similarly to `()`, the unit type.

```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

**When they're useful:** when you need to implement a trait on a type but have no actual data to store — e.g. implementing behavior where every instance of `AlwaysEqual` is considered equal to every instance of any other type (useful for a known, predictable result in testing). No fields are needed to support that kind of behavior. Traits and how to implement them on any type — including unit-like structs — are covered in Chapter 10.


---

#### Big picture

```
struct AlwaysEqual;         ← no fields at all — "unit-like"
let subject = AlwaysEqual;  ← instantiated with no brackets/parens

struct User {
    username: String,   ✅ owned — struct fully controls its data's lifetime
    username: &str,      ❌ borrowed — compiler needs a lifetime to guarantee validity
}
```


# 06-Ownership of struct Data

The earlier `User` struct deliberately used owned `String` fields rather than `&str` references — so that each struct instance **owns all of its own data**, and that data stays valid for as long as the struct itself is valid.

Structs _can_ store references instead, but doing so requires **lifetimes** (a Rust feature covered in Chapter 10) — lifetimes guarantee that referenced data stays valid for at least as long as the struct referencing it does.

**Trying to store a reference without a lifetime fails to compile:**

```rust
struct User {
    active: bool,
    username: &str,
    email: &str,
    sign_in_count: u64,
}

fn main() {
    let user1 = User {
        active: true,
        username: "someusername123",
        email: "someone@example.com",
        sign_in_count: 1,
    };
}
```

**Output:**

```
error[E0106]: missing lifetime specifier
 --> src/main.rs:3:15
  |
3 |     username: &str,
  |               ^ expected named lifetime parameter
  |
help: consider introducing a named lifetime parameter
  |
1 ~ struct User<'a> {
2 |     active: bool,
3 ~     username: &'a str,
  |

error[E0106]: missing lifetime specifier
 --> src/main.rs:4:12
  |
4 |     email: &str,
  |            ^ expected named lifetime parameter
```

Rust needs to know, at compile time, that whatever `username` and `email` point to will outlive the `User` struct itself — and without a lifetime annotation, it has no way to guarantee that. The proper fix (adding lifetime parameters like `<'a>`) is covered in Chapter 10. **For now, the practical fix is simply to use owned types** (`String`) instead of reference types (`&str`) in struct fields.

# 07-Examples on using Structs

This note walks through a single running example — calculating a rectangle's area — refactored three ways (plain variables → tuple → struct) to show _why_ structs improve clarity. It ends with how to make a struct printable for debugging via the `Debug` trait and the `dbg!` macro.

What's covered:

- The problem: two unrelated-looking parameters
- Refactor 1: grouping with a tuple
- Refactor 2: grouping with a struct (and borrowing it)
- Printing a struct: the `Debug` trait and `#[derive(Debug)]`
- The `dbg!` macro

---

## The starting point: separate variables

```rust
fn main() {
    let width1 = 30;
    let height1 = 50;

    println!(
        "The area of the rectangle is {} square pixels.",
        area(width1, height1)
    );
}

fn area(width: u32, height: u32) -> u32 {
    width * height
}
```

**Output:**

```
The area of the rectangle is 1500 square pixels.
```

**The problem:** `area` takes two parameters, but nothing in the code signals that `width` and `height` are _related_ — they're just two independent `u32`s that happen to be passed together.

---

## Refactor 1 — grouping with a tuple

```rust
fn main() {
    let rect1 = (30, 50);

    println!(
        "The area of the rectangle is {} square pixels.",
        area(rect1)
    );
}

fn area(dimensions: (u32, u32)) -> u32 {
    dimensions.0 * dimensions.1
}
```

Better — now there's one argument instead of two. But tuples don't name their elements, so you have to remember `.0` is width and `.1` is height. That's harmless for area (multiplication doesn't care about order), but it _would_ matter for something like drawing the rectangle — mixing up width and height silently produces wrong behavior, since nothing in the code conveys which index means what.

---

## Refactor 2 — grouping with a struct

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

Now `width` and `height` have names — no more guessing which index is which. Note `area` takes `&Rectangle`, an **immutable borrow**, not ownership: `main` keeps ownership of `rect1` and can keep using it afterward. Reading a borrowed struct's fields (`rectangle.width`) doesn't move those field values out — this is exactly why borrowing structs is so common.

---

## Printing a struct: the `Debug` trait

Trying to print a struct directly with `{}` fails:

```rust
println!("rect1 is {rect1}");
```

```
error[E0277]: `Rectangle` doesn't implement `std::fmt::Display`
```

> **`Display` vs `Debug`:** `{}` uses `Display` formatting — output meant for end users. Primitive types implement `Display` by default (there's only one sensible way to show a `1`), but structs don't, because there's no single obvious way to display arbitrary grouped fields (commas? brackets? which fields?). Rust won't guess, so structs get no default `Display`.

Using `{:?}` instead asks for **`Debug`** formatting — output meant for developers, not end users:

```rust
println!("rect1 is {rect1:?}");
```

```
error[E0277]: `Rectangle` doesn't implement `Debug`
```

Still an error — `Debug` isn't automatic either. You have to **opt in** by adding `#[derive(Debug)]` right above the struct definition:

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {rect1:?}");
}
```

**Output:**

```
rect1 is Rectangle { width: 30, height: 50 }
```

For larger structs, `{:#?}` ("pretty" debug) is easier to read:

**Output:**

```
rect1 is Rectangle {
    width: 30,
    height: 50,
}
```

---

## The `dbg!` macro

An alternative way to inspect values while debugging. Key differences from `println!`:

- `dbg!` **takes ownership** of the expression it's given (`println!` only takes a reference)
- prints the **file and line number** where the call happens, plus the value
- **returns ownership** of the value back, so it can be used inline without disrupting the surrounding code
- prints to **stderr**, not stdout (unlike `println!`, which prints to stdout)

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let scale = 2;
    let rect1 = Rectangle {
        width: dbg!(30 * scale),
        height: 50,
    };

    dbg!(&rect1);
}
```

Because `dbg!` returns ownership of the expression's value, wrapping `30 * scale` in `dbg!` doesn't change what `width` ends up holding — it just also prints the intermediate value. `&rect1` is passed by reference deliberately, since `dbg!` taking ownership of `rect1` itself isn't wanted here.

**Output:**

```
[src/main.rs:10:16] 30 * scale = 60
[src/main.rs:14:5] &rect1 = Rectangle {
    width: 60,
    height: 50,
}
```

The first line traces the `30 * scale` expression (line 10) and its result, `60`. The second traces the `dbg!(&rect1)` call (line 14), printing the whole struct using pretty `Debug` formatting.

> Beyond `Debug`, Rust provides several other traits usable with `#[derive(...)]` (listed in Appendix C) — implementing custom trait behavior, and writing your own traits, is covered in Chapter 10.

---

## Big picture

```
Separate vars   →   area(width, height)             ← no relation conveyed
Tuple           →   area((w, h))     dimensions.0/.1 ← grouped, but unnamed
Struct          →   area(&Rectangle) rect.width/height ← grouped AND named

Printing a struct:
  {}    → Display   → ❌ not implemented by default for structs
  {:?}  → Debug     → ✅ works once #[derive(Debug)] is added
  {:#?} → Debug (pretty, multi-line) → best for larger structs

  dbg!(expr) → prints file:line + value, returns ownership, goes to stderr
```

# 08-Methods

