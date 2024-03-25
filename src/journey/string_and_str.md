# Going back to String and &str

Let's take a quick look at the relationship between String and &str in Rust. This won't delve deeply into the intricacies but will address some key points.

```rust
fn main() {
    test_str(&String::from("hi"));
}

fn test_str(a: &str) {}
```

There is a concept known as "deref coercion" in Rust. When a type implements the Deref trait, the implementation defines what the type does when it is referenced. Therefore, whenever you create a reference from something, Deref plays a role in the referencing process.

Below is the Rust code you can refer to for the implementation, with a focus on the return type.
```rust
impl ops::Deref for String {
    type Target = str;

    #[inline]
    fn deref(&self) -> &str { // <--- look at the return type
        unsafe { str::from_utf8_unchecked(&self.vec) }
    }
}
```

Cool stuff I found today is that this works when doing comparisons as well. I was working on a palindrome problem, and my approach was to split the left part and the right part of the string (reversed), and then compare the two. Initially, my approach was to use `chars().rev().collect()`, as I assumed that was the easiest way. However, when I collected it, at first it couldn't be collected as `&str`, so I changed it to `String`, and there was no complaint from the compiler.

```rust
...
digits[0..length / 2] == digits[length / 2..].chars().rev().collect::<String>() // left is string slice, right is String
...
```

Why is it possible to compare `String` and `&str`?

```rust
#[derive(PartialEq, PartialOrd, Eq, Ord)]
#[stable(feature = "rust1", since = "1.0.0")]
#[cfg_attr(not(test), lang = "String")]
pub struct String {
    vec: Vec<u8>,
}
```

This is the source code for `String`. Ignore the fact that I was fooled into not knowing that `String` is a `Vec<u8>`. Since I'm searching for the reason of how `==` works, I'm looking at the traits. It's actually implemented as `PartialEq` and `Eq`. After looking at the source code, it's actually a macro (which I haven't taken the time to learn; I know it generates some code but nothing more). Here's the macro `impl_eq! { String, &'a str }`, assuming that this is the part where `PartialEq` implemented for `String`. For the implementation we can take a quick look <a href="https://doc.rust-lang.org/std/cmp/trait.PartialEq.html" target="_blank">here</a>

Still, I think the most important aspect, ultimately, is not an exceedingly profound mystery that I know nothing about. It feels good, man.