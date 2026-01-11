---
title: Errand Part 2 - Design
author: Elias Little
draft: true
---

This is part 2 in a series on my programming language [Errand]({{< relref "errand-lang.md" >}}). This part aims to answer how Errand aims to tackle the problems laid out in Part 1.


### Multiple Dispatch

```julia
my_func(arg)

my_func(arg<:Int)

my_func(arg::Int64)
```

### Automatic Reference Counting + Regions