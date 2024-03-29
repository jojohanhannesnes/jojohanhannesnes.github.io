# Enum and Traits for Trait Objects

When you're learning Rust, you might wonder whether to use `Enum` or `Trait Object` to make your code look better.

Let's walkthrough it with an example, first with `Enum`.
```rust
fn main() {
    let enum_q = AdderEnum::Q(1);
    test(enum_q);
}

#[derive(Debug)]
enum AdderEnum {
    Q(i32),
    W(i32),
    E(i32),
}

fn test(a: AdderEnum) {
    match a {
        AdderEnum::Q(i) => a.check(&a),
        AdderEnum::W(i) => todo!(),
        AdderEnum::E(i) => todo!(),
    }
}

impl AdderEnum {
    pub fn check(&self, example: &AdderEnum) {
        match *self {
            AdderEnum::Q(_) => example.getVal(),
            AdderEnum::W(_) => todo!(),
            AdderEnum::E(_) => todo!(),
        };
    }
}

impl ValueHolder for AdderEnum {
    fn getVal(&self) -> usize {
        println!("getting enum value for {self:?}");
        (match *self {
            AdderEnum::Q(i) => i,
            AdderEnum::W(i) => i,
            AdderEnum::E(i) => i,
        }) as usize
    }
}
```

This is quite simple, but if you modify the `AdderEnum`, you'll have to change all the code with match arms. Also, if you make this `AdderEnum` public and someone outside of your codebase tries to use it, it will be a breaking change. Now let's look at the `Trait` implementation.

```rust
pub trait Adder {
    fn add(&mut self, example: &dyn Adder);
}

pub struct X {
    pub val: usize,
}

pub struct Y {
    pub val: usize,
}

impl Adder for X {
    fn add(&mut self, example: &dyn Adder) {
        todo!()
    }
}

impl Adder for Y {
    fn add(&mut self, example: &dyn Adder) {
        todo!()
    }
}
```

In the main function, you can use it like this:

```rust
    let mut x = X { val: 1 };
    let y = Y { val: 1 };
    x.add(&y);
```

The question is how do you get a value from `example`? there's this thing called `downcasting`, meaning that it can check and refer to what is actually passed to the parameters. But there's a problem, to achieve this, what i've found is that you have to change the signature of the trait to accept `std::any::Any`. And if you do that, shit happens. Now you're allowing **`any`** struct that implement **`any kind of`** trait to be passed to the function. Look at this example.

```rust
struct Z;
pub trait ZTrait {}
```

This `Z` can be pass to the `add()` methods. Isn't that concerning? When i ask this on <a href="https://stackoverflow.com/questions/78239036/how-to-get-a-struct-value-from-a-trait-object" target="_blank">stackoverflow</a> i get a pretty good answer!

1. it's actually possible to get the value of `example`
2. my code is shit, cause it's an antipattern.

1. To get the value you have to simply add a new trait to extract that value.

```rust
pub trait ValueHolder {
    fn getVal(&self) -> usize;
}
pub trait Adder: ValueHolder {
    fn add(&mut self, sample: &dyn Adder);
}
```

2. Refering to the comments, i tried to assume this is heavily related to static vs dynamic dispatch. In short, imagine if the code is refactored to use generics instead. When we use generics, the compiler probably know what are the possibility of `T`. The size of the binary would probably be bigger because of it, but it is easy for the program to know which method to call.



>> **Note** : using trait object allow you to apply Open-Closed Principle in OOP. This means that your trait is `closed` for modification, but `open` for any extension. you can let other people from other codebases use your trait into their own struct, without sacrificing you own code. no modification needed. This doesn't mean that `Enum` is bad, everything is a tradeoff and you have to choose what is the best for the situation. you have to add the new variant everytime there's a new thing, updating match arms in every place in your code, but with `Trait`, it is considered that you're sacrificing performance, because of dynamic dispatch, in the sense that your code have to decide at runtime, what are the type that actually passed to the function