arcane:

Arc, rc, pin, unpin, Box, Cow, Arc<Mutex<T>> (Fearless concurrency),RwLock, once_cell (crate), PhantomData

 TcpStream in Tokio is designed to be Send and Sync, meaning it can be safely transferred between threads or asynchronous tasks.
 Rust ensures that types are Send and Sync by default, unless they contain interior mutability, such as Mutex or RwLock. so no need Arc<Mutex<T>> (reference to something, and bind a mutex to it, so any threads that want to mutate it need to lock to prevent race conditions) -> counter