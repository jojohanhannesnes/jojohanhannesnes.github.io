# Cow 🐄

## Cow<'a, B>
The `'a` in `Cow<'a, B>` is about the lifetime of data. It tells Cow how long the data it borrows or owns will stick around.

B, the generic parameter, is where the magic happens. B can be any type that can be both borrowed and owned.
For instance, a `&str` (a borrowed string) can transform into a `String` (an owned string) when needed. Similarly, a slice of a `Vec` can turn into a full `Vec`. This duality is what qualifies them as B.
```rust
use std::borrow::Cow;

struct Token<'a> {
    raw: Cow<'a, str>,
}

impl<'a> Token<'a> {
    pub fn new<S>(raw: S) -> Token<'a>
    where
        S: Into<Cow<'a, str>>,
    {
        Token { raw: raw.into() }
    }
}

fn main() {
    let token = Token::new(String::from("asd123"));
    let y = token.raw;
    match y {
        Cow::Borrowed(x) => println!("Y is Borrowed {}", x),
        Cow::Owned(y) => println!("Y is Owned {}", y),
    }
}
```