# Ownership and Borrowing

Hey Rust folks! Let's chat about ownership and borrowing in Rust. You've probably bumped into these terms while coding – they're like the core concepts of how Rust manages memory. Understanding them is key to grooving smoothly and be productive with Rust.

Imagine you're writing a web app, or maybe learning some other concepts in Rust, there must be a time where you encountered stuff from as simple as 
- `cannot mutate variable ???`

And escalated pretty quickly into
- `cannot move out of `???` which is behind a mutable reference`
- `??? is a `&` reference, so the data it refers to cannot be written`

We'll explore some common scenarios and decipher the associated error messages. Why the compiler says that, and what should we do.
## The Basics of Ownership

```rust
    let a = 1;
    let b = a;
    println!("{} {}", a, b);
```
Obviously a and b will hold value 1, but what if we change a to `String`?
```rust
    let a = String::from("Hello"); // move occurs because `a` has type `String`, which does not implement the `Copy` trait
    let b = a; // value moved here
    println!("{} {}", a, b); // value `a ` borrowed here after move
```
And what in the world is going on here? What's the deal with this `Copy` and `move` business? My first instinct was to dive into the crate docs. As it turns out, a numeric type like `i32` implements the <a href="https://doc.rust-lang.org/std/primitive.i32.html#impl-Copy-for-i32" target="_blank">Copy</a> trait, but `String` doesn't (Check the trait implementations in the docs, and you won't find the Copy trait listed for `String`).

Alright, so the first error line starts making sense now. Although there's a rationale behind why `String` doesn't implement the Copy trait in the first place, which is explained <a href="https://users.rust-lang.org/t/cant-derive-copy-because-of-string/18665/12" target="_blank">here</a>. For this post, I'll focus solely on deciphering the error messages.

Now, the second one involves the concept of "value moved." The `move` keyword is a way for us to transfer ownership of a variable. In the case of a string, the value from variable `a` is moved to variable `b`. That's why Rust can't print the variable `a` (as indicated by the error on the third line). In contrast, with integers, since they implement the `Copy` trait, Rust can simply copy the value to `b`, and the code runs smoothly. Let's experiment a bit further—what if we deliberately force Rust to `move` the `i32` to `b`?

```rust
    let a = 1;
    let b = move || {
        println!("Hey: {}", a);
    };
    b();
    println!("{}", a);
```

Surprisingly, this code runs! What's the deal with that? Well, it turns out that if the value implements the `Copy` trait, Rust is clever enough to opt for copying instead of moving, even when explicitly using the move keyword. This is why the println at the end of the line still works as expected.

Now, let's spice things up by introducing some mutation into our code. When crafting applications, there's a high likelihood that values will change. In Rust, we can achieve this by taking references or borrowing values using the `&` symbol. Let's explore this concept:

```rust
    let a = 1;
    let b = &a;
    b = 2;
    println!("{} {}", a, b);
```
Here, we create variable a, create a reference b pointing to a, and then attempt to mutate b. However, we encounter an error: "expected &{integer}, found integer." It appears strange, and indeed, this kind of code might not be familiar. Let's address this oddity:

```rust
    let a = 1;
    let b = &a;
    b = &2; // kind of weird, i've never actually seen this kind of code
    println!("{} {}", a, b);
```

The error is straightforward—cannot mutate variable b. In Rust, if a variable can be mutated, you must explicitly declare it as mutable using the mut keyword. Let's rectify the code:

```rust
    let a = 1;
    let mut b = &a;
    b = &2;
    println!("{} {}", a, b);
```

This code compiles, but it may seem a bit counterintuitive. Why mark b as mutable when we want to mutate a? If you've worked with other programming languages, you might be familiar with the `*` syntax for dereferencing or obtaining the value behind a reference or called a pointer. Let's try a rewrite and see if it improves clarity:

```rust
    let a = 1;
    let mut b = &a; //  `b` is a `&` reference, so the data it refers to cannot be written
    *b = 2;
    println!("{} {}", a, b);
```
Now we encounter an error: `b is a & reference, so the data it refers to cannot be written.` It may seem overwhelming, but let's break it down. b is a reference to a, indicating that the data it refers to (in this case, a) cannot be directly written. In Rust, referencing a value doesn't grant the ability to change it directly through the pointer.

To resolve this, we can create a mutable variable a and then create a mutable reference b:

```rust
    let mut a = 1;
    let b = &mut a;
    *b = 2;
    println!("{}", a);
```

You might wonder, where is b?

# Bonus 1

```rust
    let mut a = 1;
    let b = &mut a;
    *b = 2;
    println!("{} {}", a, b);
```
The error message `cannot borrow a as immutable because it is also borrowed as mutable` can be visualized as follows:

Picture yourself holding an item, let's call it `a`. Now, you grant access to another person, `b`, allowing them to modify a (while it remains in your possession since it's just borrowed). However, at the `println` statement, you decide to provide an immutable borrow of a to the `println`. Here's the catch—while someone is still borrowing your item (`a` in this scenario), you're simultaneously giving the `println` a borrow as well. However, the println operation cannot mutate the borrowed item. It's a bit weird, right?

The error signifies a fundamental issue: you cannot have both a mutable and immutable reference to the same variable within the same scope of code. To illustrate this concept, let's consider a code example following this explanation:

```rust
    let mut book = 1;
    let access_to_mutate_book = &mut book;
    let someone_trying_to_read_the_book = &book; // so far here ok
    *access_to_mutate_book = 2; // if you mutate the book, rust will complain, cannot borrow...
```

In this example, put yourself in the shoes of someone trying to read the book. If there's someone who can simultaneously mutate the book while you're trying to read it, it introduces potential inconsistencies and race conditions. As the codebase grows, predicting the order of execution becomes challenging, and the final value may become unpredictable. Therefore, Rust prohibits having both mutable and immutable references to the same variable within the same code scope, promoting code consistency and reliability.

# Bonus 2

```rust
    let a = String::from("Hello");
    let b = a; // a.clone()
    println!("{} {}", a, b);
```

When attempting to compile this code in Rust, the compiler may suggest using `clone()` instead. Rust acknowledges that using `clone()` is a viable alternative if the performance cost is acceptable, indicating that cloning can be an expensive operation. However, it's important to note that blindly opting for `clone()` is not always the desired solution, as the resulting object may not align with your intended outcome.





