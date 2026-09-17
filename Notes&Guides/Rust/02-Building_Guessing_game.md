
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

## 04-Comparing_guess_with_secret_number
here you gonna learn about kind of if-condition in rust 

added this bunch of lines:

```rust
use std::cmp::Ordering;
use std::io;

use rand::Rng;

fn main() {
    // --snip--

    println!("You guessed: {guess}");

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

this code will give you a mismatched types error since you're comparing `String` (guess) with a number (secret_number):

```bash
error[E0308]: mismatched types
  --> src/main.rs:23:21
   |
23 |     match guess.cmp(&secret_number) {
   |                 --- ^^^^^^^^^^^^^^ expected `&String`, found `&{integer}`
   |                 |
   |                 arguments to this method are incorrect
```

to fix it you alter this line:

```rust
let mut guess = String::new();

io::stdin()
    .read_line(&mut guess)
    .expect("Failed to read line");

// this one vvv
let guess: u32 = guess.trim().parse().expect("Please type a number!");

println!("You guessed: {guess}");

match guess.cmp(&secret_number) {
    Ordering::Less => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal => println!("You win!"),
}
```

explaining:

```rust
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

- `guess.trim()` strips the trailing newline the user's Enter key adds (and `\r` on Windows), leaving just the digits
- `.parse()` converts the trimmed string into a number type — but `parse` can fail (e.g. if the input isn't a valid number), so it returns a `Result`
- `: u32` tells Rust exactly which number type to parse into — an unsigned 32-bit integer, a good default for a small positive number. This also makes Rust infer `secret_number` as `u32` too, so both sides of the comparison match
- `.expect(...)` unwraps the `Result`: crashes with your message if parsing failed (`Err`), returns the number if it succeeded (`Ok`)
- this shadows the original `guess` (the `String`) with a new `guess` (the `u32`) — same variable name, new type

```rust
guess.cmp(&secret_number)
```

- `cmp` is a method that compares two values and can be called on anything comparable
- it takes a reference to the thing you're comparing against — here, `&secret_number`
- it returns a variant of the `Ordering` enum: `Less`, `Greater`, or `Equal` — the three possible outcomes of comparing two values

```rust
match guess.cmp(&secret_number) {
    Ordering::Less => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal => println!("You win!"),
}
```

- `match` takes the value returned by `cmp` and checks it against each arm's pattern in order, top to bottom
- the first arm whose pattern fits the value is the one that runs — `match` stops there, no fallthrough into the next arm

```rust
Ordering::Less => println!("Too small!"),
Ordering::Greater => println!("Too big!"),
Ordering::Equal => println!("You win!"),
```

- each of these is an **arm**: a pattern (`Ordering::Less`, etc.) on the left of `=>`, and the code to run if that pattern matches on the right
- `Ordering` only has 3 possible variants, and all 3 are covered here — if you deleted one (say `Ordering::Equal`), the code wouldn't compile. Rust forces every possible value to be handled; this is called exhaustiveness

full final code for this part:
```rust

use std::cmp::Ordering;
use std::io;

use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = guess.trim().parse().expect("Please type a number!");

    println!("You guessed: {guess}");

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

## 05-Looping
it just a simple loop braces, rust for some reason uses a while true loop that wont stop until it hit a `break` expression.
the previous code wont give the user chances to re guess so now am adding a loop for the code.
the code snippet added:
```rust
    // --snip--

    println!("The secret number is: {secret_number}");

    loop { // here it's , just a word and curly braces
        println!("Please input your guess.");

        // --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => println!("You win!"),
        }
    }
}
```

but now you got into another problem that you cant get out of that loop unless you hit `ctrl+c` but there's another way to "break" out.

it's by simply adding an extra line of code to the equal arm, see the code below:
```rust
        // --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            
            // here once it got the equal number , it prints "win" and breaks the loop
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

## 06-Handling_invalid_input
we gonna fix a problem here were the user inputs a non-numeric value would crash the game, so the fix we gonna apply here is to continue 

code snippet to explain:
```rust
        // --snip--

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {guess}");

        // --snip--

```

this replaces the old line that used `.expect(...)`, which crashed the program on bad input. Now `match` handles the error instead of crashing.
explaining:
```rust
guess.trim().parse()
```
- same as before: `trim()` strips the newline, `parse()` tries to convert the string into a number
- `parse()` returns a `Result` — an enum with two variants: `Ok` (success, holds the number) and `Err` (failure, holds error info)


```rust
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```
- this is the same `match` pattern used earlier for `Ordering`, but now matching on a `Result` instead
- two arms, one for each possible variant of `Result`

```rust
Ok(num) => num,
```

- if `parse()` succeeded, it returns `Ok` wrapping the parsed number
- `Ok(num)` is a pattern that **destructures** the `Ok` value, pulling the number out and naming it `num`
- the arm's code is just `num` — so this arm's value (the number) becomes what `guess` gets assigned


```rust
Err(_) => continue,
```

- if `parse()` failed (e.g. input was letters, not digits), it returns `Err` wrapping details about what went wrong
- `_` is the **wildcard pattern** — it matches anything and means "I don't care what's inside this `Err`, match it regardless"
- `continue` is a loop keyword: it skips the rest of this loop iteration and jumps straight back to the top of the `loop`, asking for another guess

**why this replaces `.expect(...)`:**

- `.expect(...)` on a `Result` crashes the program immediately if the value is `Err`
- `match` instead lets you decide what happens for each variant — here, `Ok` gives you the number, `Err` just retries instead of crashing
- net effect: bad input is silently ignored, and the game keeps asking until the user types a valid number