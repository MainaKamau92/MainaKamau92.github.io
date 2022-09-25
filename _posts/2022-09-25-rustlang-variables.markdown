---
layout: post
title:  "Rustlang an introduction"
date:   2022-09-25 15:52:47 +0300
categories: jekyll update
---
# Rustlang an introduction

## Variables:
Variables in Rust are immutable by default, by design as well. The immutability helps rust achieve among others the goals of safety and easy concurrency. Defining variables is pretty standard.

A variable `x` assigned to the integer `5` is shown below, although rust is statically typed in some instances like one below it can infer the data type without an explicit annotation. A semi-colon is also required indicative of statement termination.

```
let x = 5;
```

An annotated version of the variable defined above would be:

```
let x: i32 = 5;
```

We can test the immutability of variables, by trying to reassingn the x variable:

```
fn main() {
    let x = 5;
    println!("original value of x is {x}");
    x = 10;
    println!("new value of x is {x}");
}
```

Trying to compile the above code returns the error:

```
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:5:2
  |
3 |     let x = 5;
  |         -
  |         |
  |         first assignment to `x`
  |         help: consider making this binding mutable: `mut x`
4 |     println!("original value of x is {x}");
5 |     x = 10;
  |     ^^^^^^ cannot assign twice to immutable variable

For more information about this error, try `rustc --explain E0384`.
error: could not compile `playground` due to previous error
```

However, variables can be mutated by adding the keyword `mut` before the variable name. E.g.

```
fn main() {
	let mut x = 5;
    	println!("original value of x is {x}");
	x = 10;
	println!("new value of x is {x}");
}
```

Compiling and executing the code above returns the result:

```
Compiling playground v0.0.1 (/playground)
    Finished dev [unoptimized + debuginfo] target(s) in 1.21s
     Running `target/debug/playground`

original value of x is 5
new value of x is 10
```

### Constant variables
A group of immutabilities that can never be mutated are constant variables. Unlike regular variables the `mut` keyword has no effect on these. Constant variables also mandate annotation, and do not compile otherwise. They can be defined using the syntax:

```
const URL: &str = "https://mainakamau92.github.io/"
```

### Some more variables with different types
*Floating points:*

```
let flt:f32 = 3.1415;
let flt:f64 = 3.14159265359;
```

*Char (supports all unicode characters and is demarcted by single quotes):*

```
let emoji:char = '🤣';
```

*String literals:*

```
let hello:&str = "Hello, World!";
```