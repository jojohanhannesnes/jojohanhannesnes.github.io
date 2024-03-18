# Understanding Scopes and Borrowing in Rust

Scope in Rust is where the ownership and borrowing rules live, making it crucial for writing Rust code. Understanding every line of code is vital to grasping what it does. Recently, I encountered a simple yet enlightening example that provided a clearer view of borrowing and ownership scopes in Rust.

Let's delve into the code:

```rust
fn main() {
    let mut b = 1;
    let b_ref = &mut b;
    *b_ref = 3; // Mutate `b` directly
    println!("{b}");
}

fn haha(b_ref: &i32) {}
```

In this straightforward snippet, a mutable reference is used to mutate the variable `b`.

The next example passes a mutable reference to a function:

```rust
let mut b = 1;
haha(&mut b); // Move it to a function
println!("{b}");
```

Here, a mutable reference is simply passed to the function.

Now, let's explore two more code snippets:

```rust
fn one() {
    let mut b = 1;
    let b_ref = &b;
    haha(b_ref); // Modify the value through the mutable reference
    b = 3; // Mutate `b` directly
    println!("{b} {}", b_ref);
}

fn two() {
    let mut b = 1;
    haha(&b); // Modify the value through the mutable reference
    b = 3; // Mutate `b` directly
    println!("{b} {}", &b);
}
```

At first glance, these two snippets may appear similar. However, understanding each line reveals a crucial difference. The first code won't compile due to a violation of Rust's borrowing and ownership rules. The error arises from storing the reference of `b` into a variable (`b_ref`), which extends its scope beyond what's expected.

When you borrow a variable with a reference in Rust, you create an immutable borrow. This means that while the reference is in scope, you cannot mutate the original variable directly. Mixing mutable and immutable borrows violates Rust's rules.

The issue here is actually quite simple, I just wanted to try printing the reference!

So, why does the second code work? Because the function `haha` takes the scope of the immutable reference. It cannot modify it because it's behind a reference (unless you use `mut` or maybe something fancy like a `RefCell<T>`). I'm not an expert yet, so I'll leave `RefCell` for later. But the main point is, the second code works. Doesn't it feel good? Well, at least it does for me😀

In summary, when you use `&b` directly, the scope and rules of the immutable borrow apply to the function call where you pass `&b`. As long as that function call is active, you cannot mutate `b` directly. On the other hand, storing the reference in a variable (`let b_ref = &b;`) applies the scope and rules of the immutable borrow to the variable `b_ref`. As long as `b_ref` is in scope, you cannot mutate `b` directly.

So, the next time you encounter borrowing and ownership in Rust, remember to pay close attention to scopes and the rules they enforce.