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
