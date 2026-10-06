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

# Full example of all 
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