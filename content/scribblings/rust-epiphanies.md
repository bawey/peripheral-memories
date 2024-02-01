---
title: "Rust epiphanies"
date: 2023-10-19T13:43:30+02:00
draft: false
tags: [ "rust" ]
---

Learning can be fun and all but for these "it doesn't add up moments" when you need to 
take a step back and figure out where the process of knowledge assimilation took a wrong turn.
And while discovering the obvious is not too great in itself, having to re-discover it would be far worse.
So here's a note supposed to save my future self some trouble should I decide to continue learning Rust one day.

## Why reference, as in `&str`?

### In a nutshell

Because in Rust every local variable must have a known-before, type-specific size. This is true, among the others,
for integers and arrays (size is a part of array's type "signature", as in `[u8;42]`).
However, the same is not true for slices - be it array slices or string slices (technically, that's what 
[`str`](https://doc.rust-lang.org/std/primitive.str.html) is).
Under the hood, it boils down to whether a type implements
the [`std::marker::Sized`](https://doc.rust-lang.org/std/marker/trait.Sized.html) trait.

### Digging a bit deeper

Consider the following output, produced by the Rust compiler upon encountering an illegal attempt to initialize a local
variable with a value of an array slice.

```log
error[E0277]: the size for values of type `[u8]` cannot be known at compilation time
  --> src/main.rs:88:9
   |
88 |     let some_numbers_u8 = fibo_numbers_u8[3..7];
   |         ^^^^^^^^^^^^^^^ doesn't have a size known at compile-time
   |
   = help: the trait `Sized` is not implemented for `[u8]`
   = note: all local variables must have a statically known size
   = help: unsized locals are gated as an unstable feature
help: consider borrowing here
   |
88 |     let some_numbers_u8 = &fibo_numbers_u8[3..7];
   |                           +
```

The message is pretty telling and here's the code to demonstrate the relevant principle:

```rust
fn greeting() {
    let greeting: &str = "Bonjour";
    let alternatives: [&str; 4] = ["Pluto", "Mickey", "Goofy", "Donald"];
    // yes, the String type is, among other things, Sized
    let name: String = String::from(alternatives[0]);
    println!("Rust says «{}, {}. You're one of the following: {:?}»",
             greeting, name, alternatives);
}

fn numbers() {
    // implicit type [u8; 13]
    let fibo_numbers_u8 = [1u8, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233];
    // implicit type &[u8] - notice NO LENGTH and THE REFERENCE!
    let some_numbers_u8 = &fibo_numbers_u8[3..7];
    println!("Here are the Fibonacci numbers fitting into u8: {:?}", fibo_numbers_u8);
    println!("And here come just some of them: {:?}", some_numbers_u8);
}

fn main() {
    greeting();
    numbers();
}
```

So what's the deal with the `String` type itself?
Well, it's just a wrapper pointing to some data on the heap and so:
- there's nothing stopping it from being `Sized` (try printing `std::mem::size_of::<String>()`)
- since it's not a `Copy`-implementing type - it is subject to Rust's ownership mechanism
  - ⚠️ the _copy vs move semantics_ are a big thing in Rust so do yourself a favor and make sure you understand that well

For more information on basic traits, check the [`std::marker`](https://doc.rust-lang.org/std/marker/index.html) module
reference. 

