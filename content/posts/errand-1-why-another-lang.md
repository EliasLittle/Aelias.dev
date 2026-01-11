---
title: Errand Part 1 - Why Another Language?
draft: false
tags:
  - Errand
  - programming-languages
author: Elias Little
showToc: true
weight: -100
---

### Intro
Whenever I see new programming languages, the #1 question I see people ask, and one I ask myself, is why? What problems does this language solve that aren’t solved by an existing language? Why is this language the solution to those problems? Why can’t this be added to an existing language or as a library?

The most common answer to this is simply that they wanted to learn about what it takes to make a programming language. And honestly I also fall into this bucket. I had written a tiny parser in College, and had a general understanding of how interpreters and compilers worked but wanted to get my hands dirty trying to write a _real language_ [^1]. 

But beyond that, I did see lots of areas where languages currently struggle and I think we as programmers deserve better. One thing that should be mentioned is that many programming languages are decades old and, if not, new languages are often slow to adopt new features, so many of these problems and solutions are not new or novel.

### 1. The Two Language Problem 
This was the main motivation for the [Julia](julialang.org) programming language[^2]. I’m not exactly sure what the canonical source of this term is[^3], but in essence in scientific computing researchers would often write software once in a “simple” language like python to get the semantics correct, and then again in a faster language like C++ in order to get proper performance. This resulted in a lot of duplicate work that could be avoided if there was a single language that was both simple to write and had top notch performance, and thus Julia was born. And Julia is a phenomenal language, it has taken a very solid foothold in scientific computing largely solving this problem.

However, many of the tradeoffs that Julia has made in order to make it simple means that while it might be a great language for scientific computing it’s not a great choice for other types of work. The most obvious in my mind is systems work that you might use C, Rust, or even Zig for. Julia does not offer the type of low level control over memory and resources that systems languages do. 

So while Julia solved the two language problem _for scientific computing_, is does not solve the general two/multi language problem: The need for different levels of abstraction.

In my opinion there are three "levels" of abstraction that are common among popular programming languages.
1. "Systems" 
	This includes languages like C, C++, Rust, and Zig. These languages are meant for low level control of the actual hardware, manually managing memory, and getting the absolute highest level of performance. These languages are always compiled and allow for manual memory management.
2. "General Purpose" 
	This includes languages like Java, Go, and OCaml. These languages still care for good performance, but also prioritize safety and reliability over absolute performance. This class is typical for writing "enterprise software." These languages are typically compiled, but with a garbage collector[^4].
3. "Scripting"
	This includes languages like Bash, Python, Ruby, and Javascript. These support the highest level of abstraction. Users are almost entirely focused on the logic and not thinking about memory management. Languages in this category are also garbage collected and usually interpreted instead of compiled.

These different use styles demand these different feature sets for good reasons. Scripting languages demand fast feedback loops and little in the way of getting thoughts into code. You don't want to be forced to consider memory management or small differences in types. General purpose languages on the other hand want the safety of types and are happy to spend the extra time in development and compilation time to get safety and performance. Finally systems languages need direct memory control so they can't use garbage collection.

This separation of concerns has worked well for the past few decades, but is starting to show its cracks. Julia already noticed this in the scientific computing field and has had great success. But the most common solution used across many fields, most prominently in Machine Learning, is using Python while implementing the core functionality of libraries in high performance systems languages (e.g. C++ and Rust). While this does allow for the benefits of a scripting language while also getting high performance where it matters, its very rigid and requires two different types of development. There's your high level scripting language and your low level library and a rigid api between them. If you want to do something similar but not directly supported by the library your options are to either modify your higher level code and force it to match the library's api (if possible at all), or you have to go rewrite the logic in the systems language which defeats the purpose of using the abstract scripting language in the first place.

*This leads to the overarching goal of Errand*: **To give a varying level of abstraction**; As much or as little abstraction as you want, when you want it. All other goals follow this.

### 2. Easy Memory Safety & Control

One of the core differences between the three different styles of languages is how they handle memory management and safety. Memory management is extremely hard and if not handled correctly can lead to bad performance, crashes, and worse safety issues! Traditional system languages don't offer any form of safety and rely on the user to ensure safety and proper performance. General purpose and scripting languages rely on garbage collectors to help ensure safety prevent memory leaks. Garbage collection was the only major innovation around memory management for many decades, and it works splendidly for general purpose and scripted languages that prioritize safety and ease over performance and control. And nowadays garbage collection is pretty performant for most cases anyways which means there's little incentive to offer new forms of memory safety; either you accept the performance using a GC, or you resort to managing memory manually to eek out every last FLOP. 

Then came along Rust.

Rust introduced the concept of the borrow checker. A new way of statically proving memory safety at compile time. No longer forced to choose between safety and control. This has ushered in a bit of a revolution in the systems language world[^5]. This also helps bridge the gap between our system languages and general purpose languages as we now have both direct manual control over memory management and complete safety. The only remaining downside is that this still requires considerable dev-time and complexity. Manual memory management is already difficult to reason about even, and appeasing the borrow checker is notoriously difficult with an extremely high learning curve. So while this offers both precise control and safety, it does not offer the quick feedback loop and high level logic that scripting languages offer.

If we want a language to handle all abstraction layers we're going to need something that offers full control and is safe like Rust, but also easy to write high level abstractions without the language getting in your way.

### 3. Optional Hidden Behavior

High level scripting languages can be so easy to use because they do so much for the user without them knowing. The most obvious case given what we've been discussing is the fact that basically all scripting languages use garbage collection and will handle memory management automatically. But there are many other things these languages will secretly do for the user without them always knowing.
- Type Conversion
	- Converting between ints and floats is often seamless
	- Conversion is often done automatically when necessary
- Making type choices for you
	- Rust is known for have many different string types[^6], but scripting languages like Python typically only provide a single primary notion of a string and maybe a few alternatives for more advanced use.
	- Similarly instead of differentiating between `i4`, `i8`, `i16`, `i32`, and `i64`, there's often just a single `Int`
- Built-in data structures
	- Scripting languages often provide standard implementations of common data structures like dictionaries, sets, and regex
	- These are typically more opinionated, but aren't as explicit and don't always allow as much customizability as "rolling your own"

These are great when you want a scripting language as it gets out of your way. When one language might just yell at you saying that you can't add an integer and a float, telling you to cast one first, scripting languages stay out of your way and instead do their best to do what you mean. 

This doesn't mean this hidden behavior is universally great though. Sometimes you don't actually intend for this behavior and it can subtly break your program, and even if it is correct it secretly adds extra work that might be unnecessarily slowing it down. 

So how can we allow the user to choose when to allow this easier hidden behavior and when not to?

### Conclusion
These points are intended to illustrate that our current arrangement of having three different levels of abstraction within programming languages works decently well, but if you want to bridge between the different levels of abstraction, it almost always means writing different portions in different languages and calling one from the other. 

My belief is that we can write a language that has all the control and performance of C or Zig, with the safety of Rust, and the ease of Python.

Let's run some Errands.


[^1]: Whatever that means 🤷‍♂️

[^2]: A major inspiration for Errand

[^3]: [This talk](https://youtu.be/RUJFd-rEa0k?si=B9AfsvmqOISGdEgQ) covers it well. It was also likely introduced by Jeff Bezanson’s PhD thesis though it doesn’t mention it by name.

[^4]: Or other system to ensure memory safety like automatic reference counting in Swift.

[^5]: Or maybe more accurately [a meme](https://www.google.com/search?q=rewrite+it+in+rust&sca_esv=abb8d21828775dab&sxsrf=AE3TifMiM0yXHtId4Wf5ONKrqBJLxqA4Bw%3A1767402574269&ei=TmxYaYeOELSs1QG35rbpCw&ved=0ahUKEwiH7fTUl-6RAxU0VjUKHTezLb0Q4dUDCBE&uact=5&oq=rewrite+it+in+rust&gs_lp=Egxnd3Mtd2l6LXNlcnAiEnJld3JpdGUgaXQgaW4gcnVzdDILEAAYgAQYkQIYigUyCxAAGIAEGJECGIoFMgUQABiABDIEEAAYHjIEEAAYHjIGEAAYBRgeMgYQABgFGB4yBhAAGAUYHjIGEAAYBRgeMgYQABgIGB5Iyg9Q6ANYqw1wA3gBkAEAmAFNoAHcAqoBATW4AQPIAQD4AQGYAgegAqECwgIHECMYsAMYJ8ICChAAGLADGNYEGEfCAgcQIxiwAhgnwgIGEAAYBxgewgIIEAAYBxgIGB7CAgYQABgNGB7CAgcQABiABBgNwgIIEAAYBRgNGB7CAgsQABiABBiGAxiKBcICCBAAGIAEGKIEwgIIEAAYChgNGB6YAwCIBgGQBgiSBwE3oAfNLLIHATS4B5sCwgcDMC43yAcMgAgA&sclient=gws-wiz-serp).

[^6]: e.g. [this video](https://www.youtube.com/watch?v=CpvzeyzgQdw).
