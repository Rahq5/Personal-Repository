
# [An I/O Project: Building a Command Line Program](https://doc.rust-lang.org/book/ch12-00-an-io-project.html#an-io-project-building-a-command-line-program)

# Introduction
File I/O (input/output) in Rust lives in two standard library modules: `std::fs` (whole-file operations) and `std::io` (the traits and types for reading and writing data). Every operation can fail (missing file, no permission, full disk), so almost every call returns a `Result` that you must handle.

>Note: am not gonna write alot of bla-bla here and am going striaght to the point

to start using this lesson you will need to import two things:
- `use std::fs` : for reading the 
- 

## Methods for File I\O

### quick table for methods

| Method                 | Module           | Syntax                                     | Description                                                     |
| ---------------------- | ---------------- | ------------------------------------------ | --------------------------------------------------------------- |
| `fs::write()`          | `std::fs`        | `fs::write("filepath", "content")?;`       | Write a slice as the entire contents of a file                  |
| `fs::read()`           | `std::fs`        | `let v = fs::read("filepath")?;`           | Read the entire file into a bytes vector (`Vec<u8>`)            |
| `fs::read_to_string()` | `std::fs`        | `let s = fs::read_to_string("filepath")?;` | Read the entire file into a `String`                            |
| `File::create()`       | `std::fs::File`  | `let mut f = File::create("filepath")?;`   | Create a new file for writing (truncates if it exists)          |
| `write_all()`          | `std::io::Write` | `f.write_all(content.as_bytes())?;`        | Write an entire buffer into this writer                         |
| `File::open()`         | `std::fs::File`  | `let mut f = File::open("filepath")?;`     | Open a file in read-only mode                                   |
| `read_to_string()`     | `std::io::Read`  | `f.read_to_string(&mut buffer)?;`          | Read all bytes from a reader into a `String` buffer you provide |
| `remove_file()`        | `std::fs`        | `fs::remove_file("filepath")?;`            | Remove a file from the filesystem                               |
| `copy()`               | `std::fs`        | `fs::copy("from", "to")?;`                 | Copy contents (and permission bits) of one file to another      |

### explanations on each method

> **Two styles:** `fs::*` functions are one-call shortcuts for whole files. `File` + `Read`/`Write` traits give you control (partial reads, appending, buffering).

#### fs::write()

Writes the whole content in one call. Creates the file if missing, **overwrites** it if present.

```rust
use std::fs;

fn main() -> std::io::Result<()> {
    // creates "quote.txt" or replaces its old content entirely
    fs::write("quote.txt", "Stop acting so small.")?;
    Ok(())
}
```

> Note: accepts anything that is `AsRef<[u8]>` (`&str`, `String`, `Vec<u8>`).

#### fs::read()

Returns raw bytes. Pick it for binary files or when you need the bytes untouched.

```rust
use std::fs;

fn main() -> std::io::Result<()> {
    // Vec<u8>: each number is one byte of the file
    let bytes = fs::read("quote.txt")?;
    println!("{:?}", bytes);          // {:?} = Debug format, prints [83, 116, ...]
    Ok(())
}
```

#### fs::read_to_string()

Returns text. Fails with an error if the file is not valid UTF-8.

```rust
use std::fs;

fn main() -> std::io::Result<()> {
    // whole file -> String in one call
    let text = fs::read_to_string("quote.txt")?;
    println!("{}", text);
    Ok(())
}
```

#### File::create()

Opens a file for writing. If the file exists, its content is **truncated** (wiped to empty).

```rust
use std::fs::File;

fn main() -> std::io::Result<()> {
    // `mut` is required because writing changes the File's internal state
    let mut file = File::create("output.txt")?;
    // `file` is now ready for write_all() (next method)
    Ok(())
}
```

> Note: the source article claims `create` "opens the original file" if it exists. That is wrong: it truncates it. Use `OpenOptions` (see the `.txt` section) to keep old content.

#### write_all()

Pushes a full byte buffer into a writer. Needs `use std::io::Write;` or the method won't be found.

```rust
use std::fs::File;
use std::io::Write; // brings the write_all() method into scope

fn main() -> std::io::Result<()> {
    let mut file = File::create("output.txt")?;
    let message = "Hope is the thing with feathers.";

    // .as_bytes() converts &str -> &[u8], which is what write_all expects
    file.write_all(message.as_bytes())?;
    Ok(())
}
```

> Note: prefer `write_all` over `write`. `write` may write only part of the buffer and returns how many bytes it wrote.

#### File::open()

Read-only handle. Fails with `NotFound` if the file doesn't exist.

```rust
use std::fs::File;

fn main() -> std::io::Result<()> {
    // no `mut` needed yet; it becomes necessary once we call read methods
    let file = File::open("quote.txt")?;
    Ok(())
}
```

#### read_to_string() (on a File)

Reads from an open file into a `String` you own. Needs `use std::io::Read;`.

```rust
use std::fs::File;
use std::io::Read; // brings read_to_string() into scope

fn main() -> std::io::Result<()> {
    let mut file = File::open("quote.txt")?;

    let mut buffer = String::new();      // empty String that will receive the content
    file.read_to_string(&mut buffer)?;   // &mut = lets the method fill our buffer
    println!("{}", buffer);
    Ok(())
}
```

#### remove_file()

Deletes the file. Permanent: there is no trash/undo.

```rust
use std::fs;

fn main() -> std::io::Result<()> {
    // errors if the file doesn't exist or you lack permission
    fs::remove_file("quote.txt")?;
    Ok(())
}
```

#### copy()

Copies content plus permission bits. Returns the number of bytes copied.

```rust
use std::fs;

fn main() -> std::io::Result<()> {
    // overwrites the destination if it already exists
    let bytes = fs::copy("output.txt", "backup.txt")?;
    println!("copied {} bytes", bytes);
    Ok(())
}
```

### Real code examples

#### .txt file

Plain text with no structure, so every line is just a `String`.

```rust
use std::fs::{self, OpenOptions};
use std::io::Write;

fn main() -> std::io::Result<()> {
    // 1) WRITE: create (or overwrite) the file
    fs::write("notes.txt", "line one\n")?;

    // 2) APPEND: add to the end without wiping existing content
    let mut file = OpenOptions::new()
        .create(true)   // create the file if it is missing
        .append(true)   // every write goes to the end of the file
        .open("notes.txt")?;
    file.write_all(b"line two\n")?; // b"..." = byte string literal, no .as_bytes() needed

    // 3) READ: whole file back as a String
    let text = fs::read_to_string("notes.txt")?;

    // 4) ITERATE: process line by line
    for (i, line) in text.lines().enumerate() {
        println!("{}: {}", i + 1, line); // i + 1 because enumerate starts at 0
    }
    Ok(())
}
```

> Note: `append(true)` already implies write access, so the article's extra `.write(true)` is redundant.

#### .json file

JSON needs the `serde_json` crate. Add it first:

```bash
cargo add serde_json
```

```rust
use serde_json::{json, Value};
use std::fs;

fn main() -> std::io::Result<()> {
    // 1) BUILD: json! macro creates a JSON value inline
    let data = json!({
        "name": "GeeksForGeeks",
        "type": "Educational",
        "website": "https://www.geeksforgeeks.org/"
    });

    // 2) WRITE: to_string_pretty() adds indentation so the file is human-readable
    //    (use to_string() for compact output)
    fs::write("website.json", serde_json::to_string_pretty(&data).unwrap())?;

    // 3) READ: file -> String
    let raw = fs::read_to_string("website.json")?;

    // 4) PARSE: String -> Value (generic JSON tree)
    //    from_str returns a Result; unwrap() panics on invalid JSON (fine for a demo)
    let parsed: Value = serde_json::from_str(&raw).unwrap();

    // 5) ACCESS: index by key, then convert to &str
    println!("name = {}", parsed["name"].as_str().unwrap());
    Ok(())
}
```

> Note: for typed data (e.g. your `Task`), derive `Serialize` and `Deserialize` with the `serde` crate and parse straight into a struct instead of `Value`.