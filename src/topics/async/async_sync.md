# (a)sync

In the world of programming, there are synchronous processes and then there's the more advanced asynchronous paradigm. It's crucial to grasp these concepts, as they become integral in our daily coding endeavors.

## Synchronous
When you create and run a program, it becomes a process, with specific tasks to accomplish. Imagine a kitchen where you might be able to cook one meal at a time, but scalability becomes an issue when you want to use more than one pan.

In traditional programming, to handle multiple tasks concurrently, threads `std::thread` are spawned. However, as your application grows, managing these threads becomes challenging and resource-intensive.

## Asynchronous

### Approach 1 (Still Sync, Spawn Threads)
```rust
use std::io::Write;
use std::net::{TcpListener, TcpStream};
use std::thread;
use std::time::Duration;

fn handle_client(mut stream: TcpStream) {
    thread::sleep(Duration::from_secs(5));
    stream.write_all(b"hello thread").unwrap();
    println!("Connection closed");
}

fn main() {
    let listener = TcpListener::bind("127.0.0.1:8080").unwrap();
    println!("Server listening on port 8080");

    for stream in listener.incoming() {
        match stream {
            Ok(stream) => {
                println!("Accepted connection from a client");
                thread::spawn(|| {
                    handle_client(stream);
                });
            }
            Err(e) => {
                eprintln!("Error: {}", e);
            }
        }
    }
}

```
In this ***synchronous*** approach, each connection to the server blocks for 5 seconds before completing its task. To overcome this, we employ Rust's `std::thread` to spawn new threads, akin to a chef hiring assistants to expedite the cooking process. However, managing multiple threads becomes cumbersome over time.


### Approach 2 (Real Async?)
```rust

use std::time::Duration;

use tokio::{
    io::AsyncWriteExt,
    net::{TcpListener, TcpStream},
};

async fn handle_client(mut stream: TcpStream) {
    tokio::time::sleep(Duration::from_secs(5)).await;
    stream.write_all(b"hello tokio").await.unwrap();
    println!("Connection closed");
}
#[tokio::main]
async fn main() {
    let listener = TcpListener::bind("127.0.0.1:8080").await.unwrap();
    println!("Server listening on port 8080");
    loop {
        if let Ok((stream, _)) = listener.accept().await {
            println!("Accepted connection from a client");
            // handle_client(stream); // not waiting until this code finished, you wont see connection closed, cause you didnt await
            // handle_client(stream).await; // now you await this functions to finish, but this is on the main task, so its blocked, another request will be behave like "blocked by thread"
            // tokio::spawn(async { // we will try make this task as a new async task with tokio::spawn(), there's one flaw, if you await this spawn, the request will still be blocked, because we mention await which means the main thread will wait until this task spawn process finished
            //     handle_client(stream).await;
            // })
            // .await;
            tokio::spawn(async { // finally we remove the await to make sure each request will spawn the async task, without awaiting the result, we'll have the same output same behaviour with thread but with 1 thread, not multi
                handle_client(stream).await;
            });
        }
    }
}
```

Noteworthy points:

- The `loop` is essential to keep the program running as a single-threaded application.
- Initially using `handle_client(stream)` doesn't wait for the code to finish, resulting in a lack of "Connection closed" output.
- Adding `.await` on `handle_client(stream)` blocks the main task, behaving similarly to traditional synchronous programming.
- Using `tokio::spawn(async { ... })` creates a new async task, but adding `.await` on this spawn still blocks the main thread.
- Finally, removing the `.await` ensures that each request spawns an async task without waiting for the result, achieving the desired asynchronous behavior with just one thread.
- For a more clear way, try to use `(flavor = "current_thread")`, you will still have the same result 
