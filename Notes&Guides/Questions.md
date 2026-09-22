in this code why would that cause an error ? 

```rust 
fn main(){

let x = String::from("hello");

  

println!("{x}");

  

printing(x);

  

println!("{x}"); //error here , borrowing error 

}
```
