# Unlocking Rust's Magic: From String to &str to Cow

Are you just starting your Rust journey? Like many newcomers, your primary concern might have been getting your code to work without errors. But what if I told you there's a way to make your code not only work but also run more efficiently and elegantly? In this blog post, we'll explore how to transition seamlessly from `String` to `&str` and eventually harness the power of `Cow`.

## The String Struggle
Let's begin with a common scenario: writing a function that takes a String as its parameter.
```rust
fn do_stuff(val: String) {
    // implementation
}
```
To call this function, you'd need to convert your string to a `String` every time you use it, like this:
```rust
do_stuff("Hello".to_string());
```
But, seriously, do you want to clutter your code with `to_string` calls a million times? If not, read on!

## Embracing &str
You might have heard that `String` allocation on the heap is expensive because it requires `Clone`. So, to write cleaner and more efficient code, you can use `&str`

```rust
fn do_stuff(val: &str) {
    let val = val.to_string();
    // implementation
}

do_stuff(&"Hello");
```
However, there's a drawback, you can no longer pass a `String` directly. But fear not, we have a solution: similiar to this [journey](./impl_into_or_impl_from.md), we'll use `impl Into<String>`.

## The Power of `impl Into<String>`

```rust
fn do_stuff(val: impl Into<String>) {

    // implementation
}
do_stuff("im happy");
do_stuff(String::from("im also happy"));
```
As a side note, if you implement this with a regular `String`, it will consume the input because it's a `move`. This is a significant improvement, but there's more we can do to optimize.

## Introducing Cow

If you still want to keep `&str` inside your function and avoid unnecessary `String` allocation, the `Cow` (short for "clone on write") type comes to the rescue. This term might sound a bit perplexing at first, but think of it as a clever data type that clones when you want to write, while it borrows when you just need to read. In essence, these "cows" are pretty intelligent creatures, adapting to your needs seamlessly. Moo... 🐄

Think of Cow as an intelligent `into()` function. It decides whether to borrow or own the data (via Cow::Borrowed() or Cow::Owned()) based on the input:

```rust
fn do_stuff<'a>(val: impl Into<Cow<'a, str>>) {
    let x = val.into();
    match x {
        Cow::Borrowed(x) => println!("X is Borrowed {}", x),
        Cow::Owned(y) => println!("X is Owned {}", y),
    };
}
    do_stuff("asd"); // X is Borrowed asd
    do_stuff(String::from("asd")); // X is Owned asd
```
With `Cow`, you get the best of both worlds, as it can hold a borrowed `&str` and convert to an owned `String` when needed, dynamically adapting to your use case.

Now, you might wonder about the practical applications of all this. Don't worry, you're learning valuable skills, and that's what matters most on your Rust journey.

For more insights into this topic, be sure to check out the resources below:

Resources:
- [Youtube](https://www.youtube.com/watch?v=b0bgAYJDhhQ)
- [Blog](https://jwilm.io/blog/from-str-to-cow)
