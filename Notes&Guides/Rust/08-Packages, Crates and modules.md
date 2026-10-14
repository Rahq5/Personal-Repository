# Introduction 

here's where you learn to split your code up to hide un-wanted details and want the freedom to change them 
later

Core ideas to learn in this section:
- **Growth Path:** 
	  one file → many modules → many files → separate crates (external dependencies) → workspaces (Cargo feature for several related packages that change together, Chapter 14). it just shows the growth of packages and modules from small imports to large `cargo.toml` shit.
	  
- **Encapsulation:**
	  other code calls your public interface and never needs to know how it works inside. You choose what is public and what is private, so you can change the private parts later.
	  
- **Scope:**
	  the nested context where a set of names is valid. You can’t have two items with the same name in the same scope, and tools exist to resolve conflicts.

There are 4 peices of the module system :
- **Package:** 
	  A Cargo feature to build, test, and share crates. It holds many binary crates and at most one library crate.
	  
- **Crate**: 
	  A tree of modules that compiles to a library or an executable.
	  
- **Module**: 
	  Controls organization, scope, and privacy of paths.
	  
- **Path**:
	  The name of an item (struct, function, module).
	  


# Packages and Crates
This section defines the two top-level building blocks: a **crate** is what the compiler builds, and a **package** is what Cargo manages. The rules about `main.rs`, `lib.rs`, and `src/bin` decide how many crates you get.

## Crate
is the smallest unit of code the compiler considers at a time , even a single file passed to `rustc` is a crate

comes in two forms:

| Form              | Has `main`?   | Output         | Used for                                           |
| ----------------- | ------------- | -------------- | -------------------------------------------------- |
| **Binary crate**  | Yes, required | Executable     | CLI tools, servers                                 |
| **Library crate** | No            | Not executable | Functionality shared across projects (e.g. `rand`) |
- **Crate Root**: 
	  the source file the compiler starts from. It forms the root module of the crate.

## Package
- A bundle of one or more crates, described by a `Cargo.toml` file that says how to build them.
- Rules: **any number of binary crates, at most one library crate, at least one crate total.**
- Example: Cargo itself is a package holding a binary crate (the CLI tool) and a library crate the binary depends on.

**Cargo’s conventions (no config needed in `Cargo.toml`)**:

|File|Meaning|
|---|---|
|`src/main.rs`|Crate root of a binary crate named after the package|
|`src/lib.rs`|Crate root of a library crate named after the package|
|`src/bin/*.rs`|Each file is a separate binary crate|
`cargo new my-project` creates `Cargo.toml` and `src/main.rs`, so you get one binary crate called `my-project`. Add `src/lib.rs` and you have two crates (binary + library) with the same name.


# Modules
This section gives the compiler’s rules for finding module code, then shows how modules nest into a tree. The key idea is that everything in a module is **private to its parent by default**, and you opt in to exposure with `pub`.

**The 5 rules (cheat sheet)**

|Rule|What happens|
|---|---|
|**Start at the crate root**|The compiler begins at `src/main.rs` (binary) or `src/lib.rs` (library).|
|**Declare modules**|`mod garden;` in the root. The compiler looks in 3 places (below).|
|**Declare submodules**|`mod vegetables;` in any non-root file, such as `src/garden.rs`. It looks inside the `src/garden/` directory.|
|**Paths**|Refer to code anywhere in the crate, if privacy allows: `crate::garden::vegetables::Asparagus`.|
|**Private vs. public**|Items are private to the parent by default. Use `pub mod` for modules and `pub` before items inside them.|

`use crate::garden::vegetables::Asparagus;` creates a shortcut, so you write only `Asparagus` in that scope.

**Where the compiler looks for `mod garden;`**

1. Inline: `mod garden { ... }` (curly brackets replace the semicolon)
2. File: `src/garden.rs`
3. File: `src/garden/mod.rs`

For a submodule `vegetables` inside `garden`, the same 3 places apply, but under `src/garden/` (`src/garden/vegetables.rs` or `src/garden/vegetables/mod.rs`).

**The `backyard` example**

```
backyard
├── Cargo.toml
└── src
    ├── main.rs              ← crate root: `pub mod garden;`
    ├── garden.rs            ← `pub mod vegetables;`
    └── garden
        └── vegetables.rs    ← `pub struct Asparagus {}`
```

**Module tree**

- `src/main.rs` and `src/lib.rs` are called crate roots because their contents form a module named `crate` at the top of the tree.
- Relationships: if A is inside B, then **A is the child** and **B is the parent**. Modules defined in the same parent are **siblings**.
- Modules can hold structs, enums, constants, traits, and functions, not only other modules.

```
crate
 └── front_of_house
     ├── hosting   (add_to_waitlist, seat_at_table)
     └── serving   (take_order, serve_order, take_payment)
```

`hosting` and `serving` are siblings inside `front_of_house`. In the restaurant example, front of house means the customer-facing side and back of house means the kitchen and admin side.

## Code example

This example is a binary crate named `restaurant`. Each `mod` line tells the compiler which file to load, and each `use` line imports by module path, not by file path.

**File structure**

```
restaurant
├── Cargo.toml
└── src
    ├── main.rs                  ← crate root
    ├── front_of_house.rs        ← module `front_of_house` (file + folder style)
    ├── front_of_house
    │   ├── hosting.rs           ← module `front_of_house::hosting`
    │   └── serving.rs           ← module `front_of_house::serving`
    └── back_of_house
        ├── mod.rs               ← module `back_of_house` (mod.rs style)
        └── kitchen.rs           ← module `back_of_house::kitchen`
```


**`src/main.rs`**
```rust
// Crate root. A `mod` declaration here makes the compiler load the file.
mod front_of_house; // looks for src/front_of_house.rs (or src/front_of_house/mod.rs)
mod back_of_house;  // looks for src/back_of_house.rs, then src/back_of_house/mod.rs

// `crate::` = absolute path starting at the crate root (this file).
use crate::back_of_house::kitchen::cook;
use crate::front_of_house::hosting;

fn main() {
    hosting::add_to_waitlist();               // via the `use` shortcut
    cook();                                   // via the `use` shortcut
    crate::front_of_house::serving::take_order(); // full path, no `use` needed
}
```

**`src/front_of_house.rs`**
```rust
// Submodules are declared in a NON-root file.
// The compiler looks inside the folder named after this module: src/front_of_house/
pub mod hosting; // loads src/front_of_house/hosting.rs
pub mod serving; // loads src/front_of_house/serving.rs
```

**`src/front_of_house/hosting.rs`**
```rust
// `pub` is required, or other modules can't call these functions.
pub fn add_to_waitlist() {
    println!("added to waitlist");
}

pub fn seat_at_table() {
    println!("seated");
}
```

**`src/front_of_house/serving.rs`**

```rust
// Import from another DIRECTORY: absolute path from the crate root.
use crate::back_of_house::kitchen::cook;

// Import a SIBLING module: `super` = parent module (front_of_house),
// then go down into `hosting`. Same as crate::front_of_house::hosting.
use super::hosting::seat_at_table;

pub fn take_order() {
    seat_at_table();
    cook(); // the order goes to the kitchen
    println!("order served");
}
```

**`src/back_of_house/mod.rs`**

```rust
// This file IS the module `back_of_house`. Its submodules live beside it.
pub mod kitchen; // loads src/back_of_house/kitchen.rs
```

**`src/back_of_house/kitchen.rs`**

```rust
pub fn cook() {
    println!("cooking");
}
```

**Rules this shows**

- **File location is decided by where the `mod` line is written.** `mod kitchen;` inside `back_of_house` means `src/back_of_house/kitchen.rs`.
- **`use` paths follow the module tree, not folders.** You write `crate::back_of_house::kitchen::cook`, never `src/back_of_house/kitchen`.
- **Two layouts for a module with children:** `front_of_house.rs` + `front_of_house/` (newer style) or `back_of_house/mod.rs` (older style). Use only one style per module, because having both for the same module is a compile error.
- **Every step needs `pub`.** The `mod` and the item must both be visible to the caller. Here, `main.rs` declares `back_of_house` without `pub`, and that works because every module in the crate is below the root and can see it.


# Paths for Referring to an Item in the Module Tree

## Paths and Privacy (`pub`)

### Paths 
This section covers how to name an item (paths), why the compiler refuses access to private items, and how `pub` opens them up. The core privacy rule: **a child can see its ancestors, but a parent cannot see inside its children** unless the child marks the item `pub`.

**Two path forms**

|Form|Starts with|Filesystem comparison|
|---|---|---|
|**Absolute**|`crate` (same crate) or the crate name (external crate)|`/front_of_house/hosting/add_to_waitlist`|
|**Relative**|`self`, `super`, or a name in the current module|`front_of_house/hosting/add_to_waitlist`|

- Segments are separated by `::`.
- **Which to choose:** if you move the definition and the caller _together_, relative paths survive. If you move them _separately_, absolute paths survive. The book prefers **absolute**, because definitions and callers are more likely to move independently.

#### Example on Absolute and Relative

All five path starts are shown below in one library crate named `restaurant`, plus a `main.rs` that uses the external-crate form.

**`src/lib.rs`**
```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}

        pub fn seat_at_table() {
            // RELATIVE with `self`: start from the CURRENT module (hosting).
            self::add_to_waitlist();

            // RELATIVE with `super`: go up to the parent (front_of_house),
            // then down into the sibling module `serving`.
            super::serving::take_order();
        }
    }

    pub mod serving {
        pub fn take_order() {}
    }
}

pub fn eat_at_restaurant() {
    // ABSOLUTE with `crate`: start from the root of THIS crate.
    // Shell comparison: /front_of_house/hosting/add_to_waitlist
    crate::front_of_house::hosting::add_to_waitlist();

    // RELATIVE with a name in the current module: `front_of_house` is
    // defined in the same module as this function (the crate root).
    // Shell comparison: front_of_house/hosting/add_to_waitlist
    front_of_house::hosting::add_to_waitlist();

    // RELATIVE with `self`: same as the line above, but explicit.
    self::front_of_house::hosting::add_to_waitlist();
}
```

**`src/main.rs`** (same package, binary crate)
```rust
fn main() {
    // ABSOLUTE with a CRATE NAME: the library crate has the package
    // name, so from main.rs it is an "external" crate.
    // `crate::` here would point to main.rs itself, not the library.
    restaurant::eat_at_restaurant();

    // Other external crates work the same way, if listed in Cargo.toml:
    // let n: u8 = rand::random();
}
```

**Summary of the five starts**

| Start                         | Form     | Meaning                                |
| ----------------------------- | -------- | -------------------------------------- |
| `crate::`                     | Absolute | Root of the current crate              |
| `restaurant::` (a crate name) | Absolute | Root of an external crate              |
| `front_of_house::` (a name)   | Relative | An item in the current module          |
| `self::`                      | Relative | The current module, written explicitly |
| `super::`                     | Relative | The parent module                      |



### Privacy rules
- Everything (functions, methods, structs, enums, modules, constants) is **private to its parent module by default**.
- A parent **cannot** use private items inside its children. A child **can** use items from its ancestors.
- Siblings can refer to each other, which is why `eat_at_restaurant` can name `front_of_house` even though it isn’t `pub`.

**Making it compile: `pub` must be added at every step**
```rust
mod front_of_house {
    pub mod hosting {              // 1. pub on the module: lets the parent REFER to it
        pub fn add_to_waitlist() {} // 2. pub on the item: lets the parent USE it
    }
}

pub fn eat_at_restaurant() {
    crate::front_of_house::hosting::add_to_waitlist(); // absolute
    front_of_house::hosting::add_to_waitlist();        // relative
}
```

- `pub mod` alone is not enough: it only lets ancestors _reach the module_, not use what’s inside. The error changes from “module `hosting` is private” to “function `add_to_waitlist` is private” (error `E0603`).

**`super`: start from the parent module**
```rust
fn deliver_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::deliver_order(); // super = parent of back_of_house = crate root
    }
    fn cook_order() {}
}
```

- Like `cd ..` in a shell. Use it when the child and parent are likely to move together, so fewer paths need updating.

# MSG: stopped here 
i was just trying to know how to import files , further articles are not written 
to continue writing please continue from the link following the same headers shown below 

[Bringing Paths into Scope with the `use` Keyword](https://doc.rust-lang.org/book/ch07-04-bringing-paths-into-scope-with-the-use-keyword.html#bringing-paths-into-scope-with-the-use-keyword)

# Bringing Paths into Scope with the `use` Keyword

# Separating Modules into Different Files