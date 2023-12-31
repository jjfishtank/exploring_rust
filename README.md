## Table of Contents
- [Table of Contents](#table-of-contents)
- [Background](#background)
- [Analysis of Rust as a Programming Language](#analysis-of-rust-as-a-programming-language)
  - [Classification of the Language Family](#classification-of-the-language-family)
  - [Readability, Write-ability, Orthogonality, Cost, and Reliability](#readability-write-ability-orthogonality-cost-and-reliability)
  - [Implementation Method](#implementation-method)
  - [Static and Dynamic Aspects](#static-and-dynamic-aspects)
  - [Best Suited Projects](#best-suited-projects)
  - [Data Types and Control Structures](#data-types-and-control-structures)
  - [Support for Polymorphism](#support-for-polymorphism)
  - [Support for Object-oriented Programming and Inheritance](#support-for-object-oriented-programming-and-inheritance)
  - [Use of pointers/references](#use-of-pointersreferences)
  - [Memory Management](#memory-management)
  - [Functions/sub-programs Implementation](#functionssub-programs-implementation)
  - [Support for Lambda Functions and Capturing Variables](#support-for-lambda-functions-and-capturing-variables)
  - [Stand-out Features](#stand-out-features)
- [Creating a 2d game using Piston](#creating-a-2d-game-using-piston)
  - [The Process](#the-process)
  - [The End Result](#the-end-result)
- [Takeaways](#takeaways)


## Background
The purpose of this project is to explore the Rust programming language in order to analyze it's strengths and weaknesses. Going into this project, I had zero experience with Rust, so this project will double as a learning experience.

## Analysis of Rust as a Programming Language
### Classification of the Language Family
Rust is a multi-paradigm language that supports imperative, procedural, concurrent, and functional styles. Like C++, Rust is a statically typed systems programming language. It emphasizes performance and low-level detail control. Unlike C++, Rust features a borrow checker that ensures memory safety. Unlike the garbage collectors from languages like Python and Java, the borrow checker runs at compile time—eliminating the performance penalty of run-time checking.

### Readability, Write-ability, Orthogonality, Cost, and Reliability

* Readability: Rust’s syntax is designed to be clear and concise. It uses meaningful naming conventions and well-structured code. Rust makes as many zero-cost abstractions as possible.
  For example, even though Rust is statically typed, it uses type inference, reducing verbosity: `let x = 5; // infers that x is an integer`
* Write-ability: Rust supports a lot of options for abstraction when writing code. The previously mentioned type inference is one such feature. Similarly, functions infer their return values from the last expression.
  For example: `fn foo() -> i32 {
    3
}` returns 3 (the return keyword can still be used when needed.)
* Orthogonality: Rust’s features are designed to work well together, reducing the likelihood of programmer errors. For example, Rust's ownership and borrowing rules work together to prevent data races (concurrency errors from multithreading.)
  ```
  let mut x = 5;
  let y = &mut x; // y borrows x
  *y += 1; // y can modify x
  ```
  In this code, y borrows x as mutable, meaning it can change x. But while y is borrowing x, no other references to x are allowed, preventing data races.
* Cost: The cost of using Rust can be higher due to its steep learning curve. The learning curve is a bit easier than in C++, but harder than in languages like Go and Python. Its focus on safety and performance can lead to lower costs in the long run.
* Reliability: Rust’s strong static typing and ownership model ensure memory safety and thread safety, eliminating many classes of bugs at compile time. Safety is a primary concern for the language, so much so that objects are immutable by default. Memory and thread safety are ensured.
* Syntax and semantics rules: Rust’s syntax is similar to C and C++, but it enforces strict borrowing and lifetime rules to ensure memory safety. It also supports pattern matching with the match keyword: 
  ```
  let x = Some(5);
  match x {
    Some(i) => println!("Got: {}", i),
    None => println!("Got nothing"),
  }
  ```
  In this code, match is used to handle the `Option` type, which can be `Some` or `None`. This is a useful implementation of complex pattern matching that ensures safety by requiring all possible cases to be accounted for.

### Implementation Method
Rust uses ahead-of-time (AOT) compilation. Rust is a compiled language.

### Static and Dynamic Aspects
Rust is statically typed and uses a sophisticated system of lifetimes to manage memory. The borrow checker does this at compile time. It also supports dynamic dispatch through traits: 
* Trait Objects: In Rust, a trait object is created by specifying some sort of pointer (like a & reference or a Box<T> smart pointer), then the dyn keyword, and then the relevant trait. This allows us to use dynamic typing.
* Dynamic Dispatch: When you call a method on a trait object, Rust uses dynamic dispatch to determine which implementation of that method needs to be called. This decision is made at runtime, based on the actual type of the object.

Example of dynamic dispatch and traits:
```
trait Animal {
    fn make_noise(&self);
}

struct Dog;
impl Animal for Dog {
    fn make_noise(&self) {
        println!("Woof!");
    }
}

struct Cat;
impl Animal for Cat {
    fn make_noise(&self) {
        println!("Meow!");
    }
}

fn main() {
    let my_animals: Vec<Box<dyn Animal>> = vec![
        Box::new(Dog),
        Box::new(Cat),
    ];

    for animal in my_animals {
        animal.make_noise();
    }
}
```

In this code, Animal is a trait with a method make_noise. Dog and Cat are structs that implement the Animal trait. In the main function, we create a vector of Animal trait objects and call make_noise on each. The actual method that gets called (whether it’s Dog::make_noise or Cat::make_noise) is determined at runtime.

This is a powerful feature that allows for polymorphism in Rust. However, it does come with a runtime cost, and Rust’s static dispatch is more efficient.

### Best Suited Projects
Rust is well-suited for system programming, game development (although existing industry tools primarily use C++), threaded applications, and other performance-critical applications. It’s also used in web development and for creating command-line tools.

### Data Types and Control Structures
Rust has many built-in data types:
* Scalar types: signed and unsigned integers, floating point, booleans, characters, and strings.
* Compound types: tuples, arrays, and slices
* User-defined types: structs, enums, unions
* Function types: functions, closures
* Pointer types: references, raw pointers (unsafe), function pointers
* Trait types: trait objects, impl trait

### Support for Polymorphism
Rust supports polymorphism through traits and generics. Traits define shared behavior, and generics allow for code that works over many types. 
Generics are used as follows: 
`fn foo<T>(arg: T) { ... }`

An example of traits from the Rust documentation:
```
trait Printable {
    fn stringify(&self) -> String;
}

impl Printable for i32 {
    fn stringify(&self) -> String { self.to_string() }
}

fn print(a: Box<dyn Printable>) {
    println!("{}", a.stringify());
}

fn main() {
    print(Box::new(10) as Box<dyn Printable>);
}
```
"In this example, the trait Printable occurs as a trait object in both the type signature of print, and the cast expression in main."

### Support for Object-oriented Programming and Inheritance
While Rust has features similar to OOP, such as methods and encapsulation, it does not support inheritance. Rust maintains a "composition over inheritance" design choice, where traits are used to group behaviors for polymorphism. This requires some getting used to for those moving from C++ or Java.

### Use of pointers/references
Rust uses references and raw pointers for direct memory access. Raw pointers do not maintain memory safety and are discouraged. Rust also has unique pointers for heap allocation.

### Memory Management
Rust uses a system of ownership with a set of rules that make up the compile-time borrow checker. As long as unsafe operations are not used in the code, memory safety is guaranteed.

### Functions/sub-programs Implementation
Functions are declared with the fn keyword. Methods are associated functions defined within an impl block.

### Support for Lambda Functions and Capturing Variables
Rust supports lambda functions, known as closures. Closures can capture variables from their surrounding scope and are denoted with the `||` symbols. For example: 
`let add_one = |x| x + 1;
s` 
In this code, add_one is a closure that takes one argument x and returns x + 1. The `||` symbols are used to enclose the parameters of the closure.
### Stand-out Features
Rust’s most notable feature is its focus on safety without sacrificing performance. Its rich type system and ownership model guarantee memory safety and thread safety. The borrow checker is the stand-out feature here. Other notable features include support for complex match expressions and a built in package manager/builder.

## Creating a 2d game using Piston
To get hands-on experience with Rust's features, I decided to create a simple 2D game using an open source game engine for Rust called [Piston](https://www.piston.rs/). I followed Piston's [getting started](https://github.com/PistonDevelopers/Piston-Tutorials/tree/master/getting-started) page to get started.

### The Process
After installing Rust on my system and an extension for VSCode, I was ready to create the project. Rust comes with a package manager called cargo. Running the cargo new command in the terminal created the initial project files and initialized git source control for the directory. Cargo uses a simple Cargo.toml manifest file for package metadata and dependencies for compilation. Adding the dependencies I needed (such as Piston with OpenGL) was easy using the `cargo add` command.

A tool that I found to be immensely useful for coding in Rust is the built-in documentation tool. By running the `cargo doc --open` command, cargo automatically builds an html file that contains searchable documentation for all of the project's dependencies. I had to work through many syntax errors, such as not being able to access a vector with i32 integers.

### The End Result
I was able to write a very simple game that renders a 2D world map and a player that can be controlled with arrow keys. The code uses some of the features outlined in the analysis section, including lambda functions (closures), type implementation, loading and using external libraries, and complex match expressions.

## Takeaways
I have already spotted an issue where there isn't any bounds checking for the player position, so it can wander off screen. The game is clearly very barebones, but it was a good learning exercise that can be expanded upon. 

Rust's type-implementation approach was difficult to understand at first. I was stopped by many compilation errors due to the language's strict type safety. However, the error messages were generally helpful. The syntax for things like closures did not intuitively make sense to me, but it's clear that there's a lot of potential in capturing variables from within the defining scope. The pattern matching is great, and I wish C++ would implement something similar.