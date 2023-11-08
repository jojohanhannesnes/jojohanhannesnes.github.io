# Choosing How Your Code Runs: Static vs Dynamic Dispatch

Have you ever wondered how Rust programming deals with different types of data? Two methods, Static Dispatch and Dynamic Dispatch, are here to save the day. I first encountered these concepts while learning about Rust traits. So, what's all the fuss about? Well, it's all about making your code lean and clean while keeping it easy to understand.

## Static Dispatch: Double the Code, Double the Fun!
Imagine you have two separate pieces of code, like twins. Each of these codes does something slightly different based on the type of data you give it. This precision is great, but it can make your program's file size bigger.

```rust
fn do_something<T: Foo>(x: T) {
    ...
}
// once compiled become something like below
fn do_something_for_u8(x: u8) {
    ...
}

fn do_something_for_String(x: String) {
    ...
}

fn do_something_for_i32(x: i32) {
    ...
}
```

## Dynamic Dispatch: The One-Stop Code Shop
Dynamic Dispatch is like having one mega-code that does it all. It's efficient and makes your program file size smaller. This trick is done with pointers, and you'll often see the `dyn` keyword when it's used.

With Dynamic Dispatch, your code figures out what to do when it's running. It looks at the data type you're using and chooses the right action on the fly. This makes your code neater and more efficient.

Let's see an example in Rust to understand these ideas better:

```rust
// you can go to https://godbolt.org/ and search for "example::do_something:" in the compiler result
// you will see that the static implementation will have 2 section of do_something, while the dynamic will only have 1 (well i assume its a fact then)
trait Foo {
    fn method(&self) -> String;
}
impl Foo for u8 {
    fn method(&self) -> String {
        format!("u8: {}", *self)
    }
}

impl Foo for String {
    fn method(&self) -> String {
        format!("string: {}", *self)
    }
}

pub fn main() {
    let x = 5u8;
    let y = "Hello".to_string();
    // static dispatch
    do_something(x);
    do_something(y);
    // dynamic dispatch
    do_something(&x);
    do_something(&y);
}

fn do_something_static<T: Foo>(x: T) {
    x.method();
}

fn do_something_dynamic(x: &dyn Foo) {
    x.method();
}
```

It might seem that Dynamic Dispatch is the way to go. After all, you can let the compiler decide at runtime, providing a level of convenience and conciseness. However, here's the catch: there's a performance trade-off. In Dynamic Dispatch, the compiler has a tougher time determining which function to use, which can lead to slightly reduced performance compared to Static Dispatch. So, while Dynamic Dispatch is cool and powerful, it's essential to consider your specific use case and requirements.

In conclusion, the choice between Static and Dynamic Dispatch hinges on the delicate balance between code elegance and runtime efficiency. Each has its strengths and weaknesses, and the right choice depends on your project's unique needs. Happy coding! 🚀🔥