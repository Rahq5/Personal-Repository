# Defining an Enum
while struct is a grouping of multiple value, enums will be a grouping of a known set of values 

to make an enum you use the expression `enum` followed with name of the set and curly braces.
showing syntax:
```rust 
enum [name]{
	[enum 1],
	[enum 2],
	[enum 3]
}
```

real example:
```rust
enum IpAddressType{
	IPv4,
	IPv6
}
```


for further notes i will be using this enum:
```rust
enum size{
	big,
	med,
	small,
	none
}
```


## Full example of all 
full example on how to use enums (read comments)
```rust
// in IpAddressTypesEnums.rs

#[derive(Debug)]
pub enum IpAddressType{ //i added "pub" so main.rs can see the file  
	IPv4,
	IPv6
}

```

```rust
mod IpAddressTypesEnums; // declared the file
use IpAddressTypesEnums::IpAddressType; // imported the enum

#[derive(Debug)] 
    struct IpAddr {
        kind: IpAddressType, //applied it as a value type
        address: String,
    }

fn main (){
    let home = IpAddr {
        kind: IpAddressType::IPv4, // assigned it as a value "IPv4"
        address: String::from("127.0.0.1"),
    };

    let loopback = IpAddr {
        kind: IpAddressType::IPv6, // assigned it as a value "IPv6"
        address: String::from("::1"),
    };

    println!("{:?}",home); 
    println!("{:?}",loopback);
} //printed both of them 

```
outputs:
```
IpAddr { kind: IPv4, address: "127.0.0.1" }
IpAddr { kind: IPv6, address: "::1" }
```


# Enum Values 
previously we knew how to make an enum , now we gonna do some action on it 

## Assign and change values
assuming we have this enum we gonna use along all examples:
```rust
enum size{
	big,
	med,
	small,
	none
}
```

Assigning to a variables:
```rust
let fat = size::big;
let skinny = size::small;
```

changing values:
```rust
let mut fat = size::big;
println!("{?#:}",fat);

fat = size::small;
println!("{?#:}",fat);
```
output
```
big
small
```

## Pregnant enums (enums with data in them)
just kidding it's not called that but it got me confused a bit why would that even exist.

here you can fit data inside enums, if you got confused why would this be even exist, this would help in situations like IPs (see IP example) that each type of enums requiers a specific data or different treatment

see this example of how it looks like:
```rust
enum size{
	big(i32),
	med(String),
	small(human),
	none
}
//each one can hold any type of data even structs !
```

### IP scenario
we know that we have two versions of IPs, which are IPv4 and IPv6, each one of them is written in a diffrent way with showing an example:
```
IPv4 ---> 127.0.0.1 //only 4 number with limit of 255
IPv6 ---> 2001:0db8:85a3:0000:0000:8a2e:0370:7334 // full ass String
```

so to deal with that case we could define a tuple of numbers for IPv4 and a String for IPv6 like in this example:
```rust
enum ip_type{
	IPv4(u8,u8,u8,u8),
	IPv6(String)
}
```


### control menu scenario
in this scenario you could fit methods inside enums 
making this enum:
```rust
#[derive(Debug)] // lets us print with {:?}
enum Message {
    Quit,                       // no data
    Move { x: i32, y: i32 },    // named fields, like a struct
    Write(String),              // one String
    ChangeColor(i32, i32, i32), // three numbers (red, green, blue)
}

// إضافة الميثود call للـ Enum
impl Message {
    fn call(&self) {
        match self {
            Message::Quit => println!("Quit: no data, just shut down"),
            Message::Move { x, y } => println!("Move: go to x={}, y={}", x, y),
            Message::Write(text) => println!("Write: print '{}'", text),
            Message::ChangeColor(r, g, b) => println!("Color: rgb({}, {}, {})", r, g, b),
        }
    }
}
```

and this code to use the enum:
```rust
// ONE function that accepts ANY of the four shapes

fn process(msg: Message) {
    println!("Received: {:?}", msg);
    msg.call(); // الآن تعمل بنجاح!
}

fn main() {
    let m1 = Message::Quit;
    let m2 = Message::Move { x: 10, y: 20 };
    let m3 = Message::Write(String::from("hello"));
    let m4 = Message::ChangeColor(255, 0, 128);

    let messages = vec![m1, m2, m3, m4];

    for m in messages {
        process(m); 
    }
}
```
output:
```
Received: Quit
Quit: no data, just shut down
Received: Move { x: 10, y: 20 }
Move: go to x=10, y=20
Received: Write("hello")
Write: print 'hello'
Received: ChangeColor(255, 0, 128)
Color: rgb(255, 0, 128)
```

# Option enum
`Option<T>` is a standard library enum that says "there is a value, or there is nothing". Rust has no null, so this is how absence is expressed, and the compiler forces you to handle the "nothing" case before using the value.

What this note covers:

- Why Rust has no null
- How `Option<T>` is defined
- Creating `Option` values and type annotations
- Why `Option<T>` is safer than null
- Getting the value out with `match`
- Applying it to the todo list project
- Big-picture flow

---

### Why Rust has no null

Null is a value meaning "no value here". The problem is that any variable can silently be null, and using a null as if it were a real value causes an error at runtime. Tony Hoare, who invented null, called it his "billion-dollar mistake" (a costly design error), because it caused countless crashes and vulnerabilities over decades.

The idea of "absent" is still useful, so the problem is the implementation, not the concept. Rust keeps the concept but moves it into the type system.

> **Null:** a special value meaning "nothing is here", which any reference can hold in languages that have it.

---

### How Option is defined

```rust
enum Option<T> {
    None,       // no value
    Some(T),    // a value of type T
}
```

It is a regular enum. It is also in the **prelude**, so `Option`, `Some` and `None` work without importing anything or writing `Option::`.

> **Prelude:** the set of names Rust imports into every file automatically. **`<T>` (generic type parameter):** a placeholder for "any type". `Some` can hold one value of any type, and each concrete type makes a different `Option` type. Covered in book chapter 10.

---

### Creating Option values

```rust
let some_number = Some(5);          // Option<i32>, inferred
let some_char = Some('e');          // Option<char>, a different type
let absent_number: Option<i32> = None; // annotation required
```

`None` alone gives the compiler no hint about `T`, so you must write the type yourself.

---

### Why it beats null

`Option<T>` and `T` are **different types**, so the compiler refuses to treat an `Option<T>` as if it were a real `T`.

```rust
let x: i8 = 5;
let y: Option<i8> = Some(5);
let sum = x + y; // does NOT compile: cannot add Option<i8> to i8
```

The consequences:

- A plain `i8` is always valid. No null check is ever needed.
- Only values typed `Option<T>` can be absent, and you must opt in explicitly.
- Before using the inner value you must convert `Option<T>` to `T`, so you are forced to deal with `None`.

> **Opt in:** to choose to take on something explicitly, instead of getting it by default.

---

### Getting the value out

Use `match`: one arm runs only for `Some` (and can use the inner value), another runs only for `None` (no value available). `Option` also has many helper methods in the standard library docs.

```rust
fn describe(n: Option<i32>) -> String {
    match n {
        Some(v) => format!("got {}", v),
        None => String::from("nothing"),
    }
}
```

Like every `match`, it must cover both variants or it does not compile.

### Under the hood

`Option<T>` is a tagged union like any enum: a hidden tag plus space for `T`. For types that can never be zero, such as references, Rust reuses the impossible value (zero) to mean `None`, so `Option<&T>` is the same size as `&T` with no extra tag. This is called niche optimization.

> **Niche optimization:** using a bit pattern that a type can never hold to represent another state, so no extra storage is needed.
