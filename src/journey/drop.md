# Drop it baby...

```rust
#[derive(Debug)]
struct Complex {
    data: Vec<i32>,
}

impl Complex {
    fn new(data: Vec<i32>) -> Self {
        Self { data }
    }

    fn own_data(self) -> i32 {
        self.data[0]
    }

    fn own_data_vec(self) -> Vec<i32> {
        self.data
    }

    fn borrow_slice(&self) -> &[i32] {
        &self.data
    }
}

impl Drop for Complex {
    fn drop(&mut self) {
        println!("dropping Complex");
    }
}
```

as a noob in Rust, i didn't found any issue by just looking at this code, but if you try to run it, it will give an error. Remembering about Rust ownership and borrowing system, Copy and Move values, what is the issue with the code above?

if we read the error message slowly and carefully, it is pretty clear that. `own_data_vec` takes ownership of the struct, even if you only use the data, the moment you use that method, it will drop the Complex

Consider the code below, it will be error because x is dropped, moved to q

```rust
    let x = Complex {
        data: Vec::from([1, 2, 3]),
    };
    let q = x.own_data_vec();
    println!("{:?}", x); // some type's will have drop glue, meaning drop will be implemented for that type from rust itself
```

And if you want to impl Drop on struct that have method that takes the ownership of that struct, at least for now i believe it's not possible (unless you want to `.clone()`  that data), because it's a bit odd when you can moved the value and drop it as well.
