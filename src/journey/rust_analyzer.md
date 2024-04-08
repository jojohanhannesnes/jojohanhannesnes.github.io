#Tools are just Tools

This is not trying to make another project looks bad or anything, even i don't have the capacity yet to create such big things. but i was just trying to say, be careful when relying your trust on certain tools. today i found 2 simple error

```rust
use std::fmt::Debug;

fn print_size<T: Sized>(value: T) {
    println!("Size of value: {} bytes", std::mem::size_of_val(&value));
}

// ?Sized type (size not known at compile time)
fn print_trait_object_size(value: &dyn Debug) {
    println!(
        "Size of trait object: {} bytes",
        std::mem::size_of_val(value)
    );
}

fn main() {
    let num = 42;
    print_size(num);
    // let s = String::from("Hello");
    let trait_object: &dyn Debug = &num; // &s on the other hand will not give error message
    print_trait_object_size(trait_object);
}
```
```rust
struct Test {}

impl Test {
    pub fn test(e: u32) {
        println!("{}", e);
    }

    println!("{}", any_variable_that_doesnt_exist);
}
```

snippet 1, will give you an error on vscode, but passed through check, clippy, and actually compiled.
snippet 2, will *not* give you any error message on vscode, but it won't compile, the error is pretty scary though.

the idea is, it's better to deepen your understanding of the code, rather than keep on relying the tools, or compiler