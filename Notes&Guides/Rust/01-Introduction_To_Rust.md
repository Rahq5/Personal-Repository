
# 01-Introduction
This note is a quick-reference intro to Rust: what it is, what problems it solves, where it struggles, and which real companies run it in production. Read it once to get oriented, then re-read after a few weeks to refresh context fast.

**What's covered:**

- What Rust is
- Key ideas and what it helps with
- Challenges
- Who uses it
- How companies actually use it (with real cases)
- Big-picture flow diagram

---

## What Is Rust

Rust is a systems programming language (a language used to build low-level software like operating systems, browsers, and databases — the stuff other software runs on top of) created by Mozilla, first released in 2010 and stabilized in 2015. It's designed to give you the raw speed of C/C++ but without their most common source of bugs: manual memory management errors.

> **Systems programming language** — a language for writing software close to the hardware (OS kernels, drivers, browsers, databases) where performance and control over memory matter a lot.

> **Memory safety** — a program never accesses memory it shouldn't (freed memory, out-of-bounds arrays, etc.), which prevents a huge class of crashes and security holes.

Rust achieves this "under the hood" (meaning: through its internal mechanics, not something you configure manually) via a compile-time checker rather than a runtime garbage collector.

---

## Key Ideas & What It Helps With

Rust's core idea is **ownership**: every value has exactly one owner, and the compiler tracks who owns what and for how long. This is enforced by the **borrow checker** (a part of the compiler that verifies memory rules before your code is even allowed to compile).

```rust
fn main() {
    let s1 = String::from("hello"); // s1 owns the string
    let s2 = s1;                    // ownership moves to s2
    // println!("{}", s1);          // this line would fail to compile — s1 no longer valid
    println!("{}", s2);             // this works fine
}
```

What this gives you in practice:

- **No garbage collector, no manual `free()`** — memory is cleaned up automatically and predictably when the owner goes out of scope
- **Memory safety at compile time** — whole categories of bugs (use-after-free, data races, null pointer dereferences) are caught before the program ever runs
- **Zero-cost abstractions** (meaning: high-level, convenient code compiles down to something just as fast as hand-written low-level code — you don't pay a performance penalty for writing it nicely)
- **Fearless concurrency** — the same ownership rules that prevent memory bugs also prevent data races between threads, so multi-threaded code is far less risky to write

---

## Challenges

- **Steep learning curve** (meaning: it takes a lot of effort before you become productive) — the borrow checker rejects code that would run fine in other languages, and understanding _why_ takes real practice
- **Borrow checker friction** — even experienced developers regularly fight the compiler on lifetimes and ownership, especially with more complex data structures (linked lists, graphs)
- **Longer compile times** compared to Go or interpreted languages, which can slow down the write-compile-test loop
- **Smaller ecosystem** than C++, Python, or JavaScript — fewer libraries for some niche domains, though this is shrinking fast
- **Async ecosystem complexity** — Rust's `async`/`await` support is powerful but has historically had a rougher, more fragmented experience than in languages built around async from day one
- **Hiring difficulty** — demand for Rust developers has grown faster than the talent pool, making Rust engineers harder to hire than say Python developers, even as nearly half of surveyed organizations now use Rust in some production capacity, up sharply from a few years ago

---

## Who Uses It

Rust adoption has moved well past "hobby language" status. Companies known to run Rust in production include: Amazon, Google, Microsoft (and GitHub), Meta, Cloudflare, Dropbox, Discord, ByteDance, Mozilla, Apple, Figma, 1Password, Datadog, Vercel, Brave, the Tor Project, and several automakers (Volvo, Toyota, Volkswagen). For the ninth year in a row, the Stack Overflow Developer Survey named Rust the language developers most want to keep using, with an 83% admiration rate, and 12.6% of respondents had done extensive Rust development work in the past year.

---

## How Companies Use It

- **Amazon** — built **Firecracker**, the lightweight virtual machine technology that powers AWS Lambda and Fargate, in Rust for its speed and safety at massive scale.
- **Discord** — rewrote its "Read States" backend service from Go to Rust to eliminate garbage-collection pauses that were causing latency spikes under heavy load.
- **Cloudflare** — built **Pingora**, its next-generation proxy that replaced parts of its NGINX-based edge stack, in Rust to cut memory usage and avoid memory-safety bugs at internet scale.
- **Dropbox** — rewrote core parts of its file-sync engine in Rust for performance and reliability across billions of file operations.
- **Mozilla** — created Rust specifically to build **Servo**, an experimental browser engine, and now uses it in parts of Firefox.
- **Microsoft** — uses Rust for select Windows components and is investing in it as a safer alternative to C/C++ for systems code.
- Rust is now accepted directly into the Linux kernel for writing drivers, a major sign of trust from one of the most conservative codebases in existence.

Across these cases, the pattern repeats: teams had a C/C++ or garbage-collected service (Go, Python, Java) that hit a performance or safety ceiling, and moved the hot path to Rust rather than rewriting the entire system.

---

## Big Picture Flow

```
Write code with ownership rules
        │
        ▼
Borrow checker verifies memory & thread safety (compile time)
        │
        ▼
Code compiles to a native binary (no runtime/VM needed)
        │
        ▼
Program runs with no garbage collector pauses
        │
        ▼
Result: C/C++-level speed + memory safety guarantees
```


# 02-Installation_on_linux

If you’re using Linux or macOS, open a terminal and enter the following
command:
```bash
$ curl https://sh.rustup.rs -sSf | sh
```
The command downloads a script and starts the installation of the
rustup tool, which installs the latest stable version of Rust. You might be
prompted for your password. If the install is successful, the following line
will appear:
```
Rust is installed now. Great!
```


# 03-HelloWorld

Next, make a new source file and call it `main.rs`. Rust files always end with
the `.rs` extension. If you’re using more than one word in your filename, use
an underscore to separate them. For example, use hello_world.rs rather than
helloworld.rs.
Now open the `main.rs` file you just created and enter the code.
main.rs
```rust
fn main() {
println!("Hello, world!");
}
```
Listing 1-1: A program that prints Hello, world!

Save the file and go back to your terminal window. On Linux or macOS,
enter the following commands to compile and run the file:
```bash
$ rustc main.rs
$ ./main
```

```output
Hello, world!
```


# 04-Cargo
Cargo is Rust's build system and package manager and most Rust projects uses this tool.
it does:
- building your code
- downloading libraries
- building downloaded libraries

**why do i need it:**
Using Rust's Cargo simplifies your development by acting as your build system, package manager, and test tool all in one place


to make sure you installed cargo, hit this command in you shell
```shell
cargo --version
```

## 01-Creating_a_Cargo_project
creating a cargo project command:
```bash
cargo new hello_cargo --bin
cd hello_cargo
```

once you hit this command, a new folder appears with the name you chose and contains:
- **Cargo.toml:** shows Cargo's configurations written in .toml format
- **.gitignore**
- **src/**
	- **main.rs**

>**Note from Rust Dev:** 
>	rust developers expects from you to write all the logical code inside src/ , while antyhing outside is non-logical such as README.md or licenses


showing what inside `Cargo.toml`
```bash
[package]
name = "hello_cargo"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]

[dependencies]
```

- **Package:** is a section heading that indicates that the following statements are configuring a package
- **Dependencies:** is the start of a section for you to list any of your project’s dependencies

## 02-Building and running cargo
after cargo got created, you have to build it and run so basically to build the code use this commands:
```bash
cd hello_cargo
cargo build
```
outputs:
```bash
 Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.00s
```

here you will get a new file under this path which is the binary (executable) file `hello_cargo/target/`.
generating a binary file means this file can be handed to any processor and it will execute it even if rust wasnt installed on that machine, not likely with python and javascript that requires for these languages to be pre-installed

now to run the binary file type this command:
```bash
./target/debug/hello_cargo
```
output:
```bash
Hello, world!
```

also to build and run in the same time just hit 
```bash
cargo run 
```
outputs:
```bash
 Compiling rust_project v0.1.0 (/home/rahq05/vscode_expermints/Rust/rust_project)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.04s
     Running `target/debug/rust_project`

Hello, world!
```



# 05-Building_GuessingGame
## 01-Building_code
```rust
use std::io; // import I/O library from standard library

fn main() {
    println!("guess the number! ");
    println!("plz input your number: ");

    let mut guess = String::new(); // mutable empty String

    io::stdin()
        .read_line(&mut guess) // read input into guess by reference
        .expect("failed to read lines"); // crash with message if Err

    println!("you've guessed: {guess}");
}
```

```rust
use std::io;
```

- `use`: brings a module/library into scope so its items can be used without writing the full path every time
- `std::io`: the I/O module from Rust's standard library; handles input/output operations like reading from the terminal


```rust
let mut guess = String::new();
```

- `let`: declares a new variable
- `mut`: makes the variable mutable; without it, variables in Rust are immutable by default and cannot be changed after being assigned
- `String::new()`: creates a new, empty, growable `String`


```rust
io::stdin()
```

- `io::stdin()`: calls the function that returns a handle to standard input, meaning access to what's typed in the terminal


```rust
.read_line(&mut guess)
```

- `.read_line(...)`: reads a line of input from stdin and appends it into the given `String`
- `&mut guess`: passes a mutable reference (a pointer) to `guess` instead of the whole `String`; this avoids copying the full data into the function, and instead lets `read_line` access and modify the same memory directly


```rust
.expect("failed to read lines");
```

- `.expect(...)`: a method on `Result`, which can be `Ok` (operation succeeded) or `Err` (operation failed, usually carrying a message explaining what went wrong)
- if the `Result` is `Err`, `.expect()` crashes the program and prints the message passed to it


```rust
println!("you've guessed: {guess}");
```

- `println!`: macro that prints text to the terminal followed by a newline
- `{guess}`: inserts the current value of the `guess` variable directly into the printed string

## 02-Random numbers (adding dependencies using crates)

**crate:** is a collection of rust source code files 

here where cargo's actually shines , you're now about to add your first ever dependency.
go to `cargo.toml` under `[dependencies]` and add this line which end up like this:
```rust 
[dependencies]
rand = "0.8.5"
```

then hit this command to see the crate actually gets added:
```shell
crate build
```
output:
```shell
rahq05@RawiUbuntuPC:~/vscode_expermints/Rust/guessing_game$ cargo build 
   Compiling libc v0.2.189
   Compiling cfg-if v1.0.4
   Compiling zerocopy v0.8.57
   Compiling getrandom v0.2.17
   Compiling rand_core v0.6.4
   Compiling ppv-lite86 v0.2.21
   Compiling rand_chacha v0.3.1
   Compiling rand v0.8.8
   Compiling guessing_game v0.1.0 (/home/rahq05/vscode_expermints/Rust/guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 2.20s
```

## 03-Generating_Random_Numbers
here am gonna add a new line of code to the original one which is:
```rust
use std::io; // import I/O library from standard library
use rand::Rng; //importing random library

fn main() {
    println!("guess the number! ");
    println!("plz input your number: ");

    let mut guess = String::new(); // mutable empty String
    
    // the new part vvv
    let secret_num = rand::thread_rng().gen_range(1..=100);

    io::stdin()
        .read_line(&mut guess) // read input into guess by reference
        .expect("failed to read lines"); // crash with message if Err

    println!("you've guessed: {guess}");
}
```

explaining:
```rust
let secret_num = rand::thread_rng().gen_range(1..=100);
```

```rust
let secret_num =
```
- here you made a mutable (unchangeable) variable 

```rust
rand::thread_rng()
```
- function that gives us the particular random number generator we’re going to use: one that is local to the current thread of execution and is seeded by the operating system

```rust
.gen_range(1..=100);
```
- this function takes the range of random number as an argument using this expression `(start..=end)`

