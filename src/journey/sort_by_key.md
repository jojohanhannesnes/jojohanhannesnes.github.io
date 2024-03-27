# Nested Sorting: A Cool Trick with sort_by_key

Imagine you have a list of numbers and you want to sort them in a special way: 

first, you want all the odd numbers to come before the even numbers, and then within each group, you want the numbers to be sorted in ascending order. Sounds complicated isn't it? In Rust, you can achieve this using the `sort_by_key` method along with a closure that defines how to extract and compare keys.

```rust
fn main() {
    let mut v = vec![8, 17, 4, 9, 22, 31, 16, 3, 12, 5];
    v.sort_by_key(|&v| (v % 2, v));
    println!("{:?}", v); // [4, 8, 12, 16, 22, 3, 5, 9, 17, 31]
}
```

 - sort_by_key: This is a method provided by Rust's standard library for sorting a collection based on a key extracted from each element. It takes a closure that extracts a key from each element and uses that key for sorting.

 - `(|&v| (v % 2, v))`: This is the closure passed to sort_by_key. It takes an integer v (which is a reference due to &) and returns a tuple `(v % 2, v)`.

 - `(v % 2, v)`: This tuple serves as the key for sorting. The first element v % 2 is the remainder of v divided by 2, which effectively groups even and odd numbers together. The second element v itself ensures that within each group, the numbers are sorted in ascending order.

So, for example, if v is `[3, 7, 2, 5, 8, 4]`, the closure would return `[(1, 3), (1, 7), (0, 2), (1, 5), (0, 8), (0, 4)]`, where odd numbers come before even numbers, and within each group, numbers are sorted in ascending order.

> **Note**: <br>***The sort_by_key method uses the keys returned by the closure to sort the vector v. <br><br>Since the keys are tuples, the sorting happens in two steps:<br> - First, based on the first element of the tuple (v % 2), which groups the numbers.<br> - Second, based on the second element (v), which sorts the numbers within each group.***

