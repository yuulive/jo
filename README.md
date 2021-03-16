# jo

Jo is a procedural macro for Rust that allows you to ensure that functions are not
running at the same time. This is especially useful for integration tests,
where tests that are writing to the same database table should not run in
parallel.

The goal of mutual exclusion is achieved by acquiring a mutex at the beginning
of the annotated function. So in essence this macro is syntactical sugar for
writing `MUT.lock().unwrap()` at the beginning of every function.

Different functions can synchronize on different mutexes. That's why a
static mutex reference must be passed to the `jo` annotation.


## Usage

```rust
use std::sync::Mutex;
use lazy_static::lazy_static;
use jo::jo;

// Create two locks
lazy_static! { static ref MUT_A: Mutex<()> = Mutex::new(()); }
lazy_static! { static ref MUT_B: Mutex<()> = Mutex::new(()); }

// Mutually exclude parallel runs of functions using those two locks

#[jo(MUT_A)]
fn function_a1() {
    // This will not run in parallel to function_a2
}

#[jo(MUT_A)]
fn function_a2() {
    // This will not run in parallel to function_a1
}

#[jo(MUT_B)]
fn function_b() {
    // This may run in parallel to function_a*
}
```


## License

Licensed under either of

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or
   http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or
   http://opensource.org/licenses/MIT)

at your option.
