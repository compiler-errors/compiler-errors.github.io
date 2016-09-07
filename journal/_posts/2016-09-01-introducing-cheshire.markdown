---
layout: post
title: "Introducing Cheshire"
tags: [programming, rust, llvm]
---

A while back, I tried to write a toy programming language and compiler in order to learn more about:

* How compilers are written
* How type systems work
* How compilers use technologies like LLVM
* How to write efficient C/C++

I called my toy programming language `Cheshire`. It never really got anywhere because it quickly devolved into messiness, due to starting out writing the project in C and quickly realizing that C++ features were greatly desired, and thus half of the project needs to interface with C code. The project also tried to output plain LLVM IR code as opposed to using LLVM's own interfaces, which led to some irrepairable problems.

Recently, I have been getting interested in writing compilers again, and I've decided to give another shot at Cheshire. Having programmed extensively recently in Rust, I decided to write the compiler in Rust, knowing I could take advantage of the features that the language gave me (pattern matching, type inference, strict memory model) in order to make writing the compiler much more fun and easy. 

# The Language

Cheshire is inspired in part by Rust, Java, and C++. Variables are declared with `let`, type annotations being optional within function definitions, and functions are declared with `fn` followed by a name, parameters, and optinally a type. Here's an example function:

```
fn fibonacci(i: UInt32) -> UInt32 {
    if i == 0 | i == 1 {
        return i.
    }

    return fibonacci(i - 1) + fibonacci(i - 2).
}
```

We can see that statements end in a '`.`', but otherwise the syntax is not too unfamiliar to anyone who has dealt with C++ or similar languages.

In Cheshire, there are objects, and all objects are dealt with as _references_, like in Java, with automatic garbage collection and the likes.

```
object Foo {
    has i: Int32.
    has j: String.

    new(i: Int32, j: String) {
        self:i = i.
        self:j = j.
    }

    fn get_i() -> Int32 {
        return self:i.
    }

    fn get_j() -> String {
        return self:j.
    }
}
```

Object members are accessed with `:` (and that's how methods are invoked as well).

With those examples stated, I won't go too much deeper into how Cheshire's syntax currently stands. I plan on adding to the grammar a system similar to Rust's `trait`s, type constraits on generics, and a whole bunch of other random things which I will hopefully write into a post in the future.

# Why Rust?

So why did I write this project in Rust?

I've currently written the Lexer and Parser stages of the compiler, and thus have had an opportunity to reflect on how Rust has helped me throughout the lifetime of this project. I'll go into detail on separate posts about the specifics of writing the Lexer and Parser stages, but I want to talk about how Rust has helped (and hindered) my coding in general in this part of the post.

### The Match Idiom 

The powerful `match` keyword is one of Rust's favorite features, since it shows up everywhere in Idiomatic Rust code. I use it extensively in the Lexer and Parser, and will probably do the same in Type Checker as well. Paired together with Rust's Enums, it leads to very efficient code in cases where branching is necessary and extensive.

### The `?` Operator

While not a stable feature (yet), the `?` operator and the `Result<T, E>` enum form a really interesting and safe way to propagage errors up the call stack, which is very important in a parser where I find myself going pretty deep into nested helper functions. It means I can write functions like this:

```
/// Parse the `new` constructor for Cheshire `object` definitions.
fn parse_obj_constructor(&mut self) -> ParseResult<AstObjectMember> {
  self.expect_consume(Token::New)?;
  let parameter_list = self.parse_fn_parameter_list()?;
  let definition = self.parse_block()?;
  Ok(AstObjectMember::constructor(parameter_list, definition))
}
```

Which, in my opinion, makes for really pretty parsing.


# Conclusion

I definitely didn't get all of my thoughts out in this post. I hope to follow up with a few more posts as I continue writing this language, hopefully to go into more detail about how my compiler works, the things I run into while dealing with Rust, and just programming in general.

Thanks for reading!
