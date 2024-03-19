# The Dangers of Wildcards in Rust Enums: A Lesson from Logan_Smith

In Rust, enums provide a powerful way to define a type with a fixed set of possible values. However, using wildcards in match statements with enums can lead to unexpected behavior and potential bugs. This insight was inspired by Logan_Smith's YouTube series on Rust programming, particularly [this video](https://www.youtube.com/watch?v=8j_FbjiowvE&list=PLhjB8nmMLotIG0ik1RXjl0lfZcg9oxhMg).

Let's explore this issue with an example:

```rust
use Status::*;

enum Status {
    Pending,
    Success,
}

fn main() {
    let x = Status::Pending;
    match x {
        Pending => todo!(),
        Success => todo!(),
    }
}
```

In this example, we have an enum `Status` with two variants: `Pending` and `Success`. The `match` statement is used to match the value of `x` against the enum variants. However, if we were to change the enum to include a `Failed` variant and forget to update the `match` statement:

```rust
enum Status {
    Pending,
    Failed,
}
```

The `match` statement will still compile, but the `Success` variant will act as a catch-all pattern, matching any value that is not explicitly handled. This can lead to unintended behavior and make it difficult to spot bugs.

To mitigate this issue, we can use aliases to ensure that all enum variants are explicitly matched:

```rust
use Status as S;

enum Status {
    Pending,
    Failed,
}

fn main() {
    let x = S::Pending;
    match x {
        S::Pending => todo!(),
        S::Failed => todo!(),
    }
}
```

By using aliases (`S::Pending`, `S::Failed`) instead of the enum variants directly, we ensure that all variants are accounted for in the `match` statement. This makes the code more robust and easier to maintain, especially when the enum is updated with new variants.