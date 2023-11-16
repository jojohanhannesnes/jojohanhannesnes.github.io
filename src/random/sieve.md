# Party with Eratosthenes! 
I'm diving into the thrilling world of Rust via Project Euler, thinking I've got it all under control. Then, bam! The 10th problem hits me like a curveball—summing prime numbers up to a cool 2 million.

My initial plan? The ol' brute force. I mean, why not? Rust is speedy, right? Well, time to bite my fingernail 💅🏻 cause my code decides to take a nap instead of hustling.

Enter my coding superhero moment! I hit the virtual streets, not for a ready-made solution but for some cryptic hints. And guess what emerges? The Sieve of Eratosthenes!

I tune into a [Khan Academy video](https://www.youtube.com/watch?v=klcIklsWzrY), and there I am, laughing at the simplicity of it all. Armed with Eratosthenes' secret sauce, my code transforms from a sluggish snail to a sonic. A quick test run with a 10k limit, and voila! The runtime goes from a yawn-worthy 6 seconds to a jaw-dropping 7 milliseconds. Take a bow, Eratosthenes!

```rust
// Code in slow-motionnnn
pub fn is_prime(number: &usize) -> bool {
    (2..*number).all(|x| number % x != 0)
}

fn prime_10001() -> usize {
    let mut counter = 0;
    let mut result = 0;
    for i in 2..=usize::MAX {
        if counter == 10001 {
            break;
        }
        if is_prime(&i) {
            result = i;
            counter += 1;
        }
    }
    result
}
```

```rust
// b...b...bLAAAZINGLY FASTTTT!
fn prime_10001_sieve() -> i32 {
    const LIMIT: usize = 10001;
    let mut sieve = vec![true; LIMIT];
    let mut count = 0;

    for num in 2..LIMIT {
        if sieve[num] {
            count += 1;

            if count == 10001 {
                return num as i32;
            }

            for multiple in (2 * num..LIMIT).step_by(num) {
                sieve[multiple] = false;
            }
        }
    }

    0
}
```

The tests... (7th and 10th problem)

```rust
    #[test]
    fn get_10001_prime() {
        let start_time = Instant::now();
        let result = prime_10001();
        let elapsed_time = start_time.elapsed();

        println!("Result: {}", result);
        println!("Time taken: {:?}", elapsed_time);
        assert_eq!(prime_10001(), 104743)
    }

    #[test]
    fn get_10001_prime_sieve() {
        let start_time = Instant::now();
        let result = prime_10001_sieve();
        let elapsed_time = start_time.elapsed();

        println!("Result: {}", result);
        println!("Time taken: {:?}", elapsed_time);
        assert_eq!(prime_10001(), 104743)
    }

    /*
    ---- euler::prime_10001::tests::get_10001_prime_sieve stdout ----
    Result: 104743
    Time taken: 7.579443ms

    ---- euler::prime_10001::tests::get_10001_prime stdout ----
    Result: 104743
    Time taken: 6.726276021s
    */
```

```rust
pub fn sieve_of_eratosthenes(limit: usize) -> Vec<usize> {
    let mut is_prime = vec![true; limit];
    is_prime[0] = false;
    is_prime[1] = false;

    for i in 2..((limit as f64).sqrt() as usize + 1) {
        if is_prime[i] {
            for j in (i * i..limit).step_by(i) {
                is_prime[j] = false;
            }
        }
    }

    (2..limit).filter(|&x| is_prime[x]).collect()
}

fn summation_of_primes(limit: usize) -> usize {
    sieve_of_eratosthenes(limit).iter().sum::<usize>()
}

#[test]
fn test() {
    use std::time::Instant;
    let start_time = Instant::now();
    let result = summation_of_primes(2_000_000);
    let elapsed_time = start_time.elapsed();

    println!("Result: {}", result);
    println!("Time taken: {:?}", elapsed_time);
    assert_eq!(summation_of_primes(2_000_000), 142913828922)

    /*
    ---- euler::summation_of_primes::test stdout ----
    Result: 142913828922
    Time taken: 133.579135ms
    */
}

```

Here's the concept: Starting from 2 up to a specified limit, i initially label everything as true. Then, as i traverse each number, if it holds true, i embark on a journey through its multiples until reaching the limit, marking each one as false. The brilliance lies in the fact that if, for instance, 2 is prime, this process automatically declares anything divisible by 2 as non-prime (composite). It's a straightforward and clever approach.

In the end, what began as a Rust adventure turned into an epic optimization quest, where Eratosthenes stole the show from my initial brute force bravado. The code now stands as a celebration of clever algorithms and the sheer joy of making my code dance to their beats. Happy coding, party people! 🚀💻
