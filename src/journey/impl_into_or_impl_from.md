# Unlocking the Magic of impl Into and impl From

Today, I embarked on a journey to create a dynamic return statement for a Rust function, and the code quickly became a labyrinth. Here's what I started with:

```rust
fn function() -> ??? {

}
```
To bring some structure and flexibility (i guess?), I decided to introduce an enum called Response:
```rust

enum Response {
    String(String),
    Number(i64),
}

fn function() -> Response {
    ...
    Response::String(String:from("response")) // Response::Number(100) will also works
}

```

While the enum certainly tidied things up, I couldn't help but wonder if there was a more elegant solution. What if we could seamlessly transform a value into the desired return type, without all this boilerplate? Enter `Into`.

`Into` is a trait that performs type conversion, provided there's an implementation for it. It's like magic, allowing you to turn one type into another with minimal effort. But it comes with a twist – there's also `From`. Both of these traits seem to do the same thing, but there's a subtle difference.

`Clippy` has an opinion on this matter:

> An implementation of `From` is preferred since it gives you `Into<_>` for free where the reverse isn't true.

This nugget of wisdom tells us that if we implement `From`, we automatically inherit `Into`. The reverse, however, is not true. <br><br>Here's a demonstration:

```rust
// impl Into<Response> for i64 {
//     fn into(self) -> Response {
//         Response::Number(self)
//     }
// }
// 12_i64.into() ✅
// Response::from(12_i64) ❌

impl From<i64> for Response {
    fn from(value: i64) -> Self {
        Response::Number(value)
    }
}
// 12_i64.into() ✅
// Response::from(12_i64) ✅
```

There's also another thing called `TryFrom` and `TryInto`, wasn't sure if they have the same exact behaviour, all i know is that they are fallible (meaning they return `Result` type), probably for better error handling