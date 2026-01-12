---
title: Errand Part 2 - Design
author: Elias Little
draft: true
---

This is part 2 in a series on my programming language [Errand]({{< relref "errand-lang.md" >}}). This part aims to answer how Errand aims to tackle the problems laid out in Part 1.

Our first solution is directly inspired by Julia. 
### Multiple Dispatch

One of Julia's key paradigms is using [Multiple Dispatch](https://en.wikipedia.org/wiki/Multiple_dispatch), which effectively chooses method implementations based off the types of all the arguments provided. This is in contrast to most languages that either have a single implementation for each function name, or allow single dispatch allowing for different methods based on a single argument. Single dispatch is very common in object oriented languages since objects can own methods themselves. So you can have patterns like `A.method()` and `B.method()` where `A` and `B` are different types, which means that the `method()` function is dispatched based on the first argument, in this case the object that's calling it. Operator overloading is also a common pattern that allows for different implementations for a handful of unary and binary operators. Multiple dispatch allows for choosing the implementation based on the types of all arguments for any function.

Multiple dispatch is extremely powerful, it is a known solution to the [Expression Problem](https://en.wikipedia.org/wiki/Expression_problem), and has been shown to lead to high levels of code reuse and other benefits in the Julia community [^1].
{{< details summary="Aside on the expression problem" >}}
>Quick note on the expression problem. In brief, it's the issue where in basically every language it's either really easy to either extend existing data types, or to add new functions operating on those data types, but not both.
> 
>This is extremely evident once you start working on any moderately complex system in basically any language. In most object oriented languages, they allow easy extension of types, but not functions on existing ones. If there's an existing library that you want to add functionality to, you'll need to create your own type that inherits from the library type and then add new functions to it. But this means that any other code that's aware of the original library no longer works with your version of the type. 
>
>Multiple dispatch solves this by being able to define new functions on existing types outside of the type's definition. This is what helps contribute to the high levels of code reuse in Julia. 
{{< /details >}}

All of this is already excellent, and a great reason to use multiple dispatch, however it also unintentionally enables additional behavior that extremely beneficial for us: It allows a variable amount of specificity. 

We are able to call the function by just passing the argument and allowing the compiler to figure out the right implementation allowing for a little bit of hidden behavior or "magic", or we're able to specify exactly which implementation we want by fully typing the variables. We're also able to get levels of specificity in between, we can get some safety by specifying a subtype relationship while still allowing a little bit of hidden behavior to allow for more ease. All of the following calls are completely valid and offer very different levels of hidden behavior:

```julia
# Written in Julia

my_func(a, b)

my_func(a::T, b::S) where {T <: Integer, S <: Number}
# or equivalently my_func(a::Integer, b::Number)

my_func(a, b::Float64)

my_func(a::Int64, b::Float64)
```

In Julia, specifying the type acts as an assertion verifying that the variable does indeed have that type, but also means the compiler will always choose the implementation that matches. This means that every method is completely specified by its name and the types of all of its arguments.

This means we can use function dispatch as an interface for hiding complicated behavior. With a combination of optional arguments and multiple dispatch we can allow the user to specify whether they want the system to handle things automatically or whether they want to specifically choose all the details.

This won't cover every case of hidden behavior, but it's a very simple way of giving the user a variable amount of control.

### Type System

Given that Multiple Dispatch is very dependent on its type system this is also a very important part of the language.

### Automatic Reference Counting + Regions

As mentioned in part 1, memory management is one of, if not _the_, biggest differentiators between the levels of language abstraction. Do we allow direct control over memory, do we use a garbage collector, do we use Rusts' fancy borrow checker?

The absolute non-negotiables are both:
- The ability to have complete manual control over memory
- The ability to have the system automatically handle memory

This eliminates the C-style where the user has control but is always required to manage memory.

In order to have both, we'll likely need to have a setting in the compiler that will enable/disable automatic memory management when it is (not) desired. 

#### Automatic Reference Counting

Our choice of automatic memory management is now between using a garbage collector and automatic reference counting (ARC). Garbage collection is by far the more popular choice, and is generally more performant, however it is also more complicated and can lead to less predictable performance. While generally less performant, automatic reference counting has more predictable performance as it doesn't have long pauses for cleanup and it frees memory as soon as its able to be, unlike garbage collection. Using ARC is also much clearer to the user what the system is doing in order to keep track of and free memory.

We will also see shortly that there are certain things we can do to even eliminate the need for any reference counting in certain regions further improving the performance while still guaranteeing memory safety.

For these reasons, we will move forward with automatic reference counting over garbage collection.

#### Regions
For a quick definition, a region is a singular contiguous section of code. Think of a function body, or the body of a loop or if statement, for the most common examples.

If we're able to tell the compiler certain information about our region then we can eliminate any reference counting in the region, bringing performance back on par with manually managed systems languages. 

Reference counting fundamentally tracks the number of live references to a specific piece of heap allocated memory. We only need to increment or decrement ref. counts when we're creating new references stored somewhere, or removing existing references. This means that if we can guarantee to the compiler that we don't do any of these, then we know we don't need to bother doing any reference counting in this region.

Say we're working with the body of a function (the most common region), if we know that the arguments are references to memory, and we know that we don't store this reference in any way [^2], then we know that we can skip all reference counting for the region as we know it doesn't store new references or remove existing ones[^3].

We also know that if within a region, all of the operations are free of reference updates, then this region is as well. 

Another optimization that can be made is if we know a particular variable only has one live owner then we know that we can skip incrementing on moves, and only need to decrement once on destruction[^6]. This relates closely to _Linear_ or _Affine_ "types" which we'll get to later, but are extremely powerful, and can help us a lot when it comes to memory (and resource) management.

Additionally, even in cases where we have to modify reference counts, we can improve the performance by telling the compiler when these can be batched or when they can be reordered. For example if we're using a reference in a loop, then a naive implementation might increment and decrement on each loop iteration by default, but it's much more efficient to do all of the increments at once before the loop, and all the decrements at once after the loop.

There are lots of other scenarios, where if we're able to provide the compiler with a little extra information then it is able to remove ref count operations entirely, or at least optimize/batch them.

The question then becomes, how do we convey this information to the compiler? 
The answer: **Modes**.

### Modes

Modes are relatively new and not present in many popular languages. One of the most recent pushes is actually in OCaml thanks to the wonderful folk at Jane Street[^4], and this can be seen in their [OxCaml](https://oxcaml.org/) extension of the language [^5].

In brief, modes are additional constraints that we put on individual variables and functions that enforce very specific behaviors. These behaviors can allow and enforce many useful properties around performance and safety, and are not just useful for reducing/eliminating reference counting as mentioned in the previous section, but also help the user reason about performance in a very clear way.





[^1]: See this _excellent_ talk from Stefan Karpinski (one of the creators of Julia): [The Unreasonable Effectiveness of Multiple Dispatch](https://www.youtube.com/watch?v=kc9HwsxE1OY)

[^2]: This can get a little complicated as it includes storing in a global variable, or in some other argument that escapes the region like a mutable Array, but it also includes storing it in a closure and other more complicated situations.

[^3]: This is greatly over simplifying, there are many conditions that need to hold in order to completely skip reference counting. For example not only do we have to ensure the arguments aren't stored, but we also have to ensure that all variables declared within the function aren't stored and don't escape the region.

[^4]: Disclamer: I once worked at Jane Street, but I never worked on the languages and compilers team nor directly with any of the members of the team. However, I can vouch that it's a great place to work, especially if you're interested in working on languages like this.

[^5]: They also have multiple talks on the matter which I recommend, in addition to [this series](https://www.youtube.com/watch?v=LwD3GxsY-pc&list=PLCiAikFFaMJrgFrWRKn0-1EI3gVZLQJtJ) exploring some of the Modes, primarily the new `local` mode.

[^6]: Technically, we don't actually need to decrement at all as long as we just run the destructor since we know this is the only live owner and therefor there are no other consumers of the ref count.
