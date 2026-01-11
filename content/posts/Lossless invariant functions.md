---
title: "Lossless invariant functions"
date: "2025-02-21"
draft: true
showToc: true
TocOpen: false
---


For geometric deep learning we list our invariants that we want to hold for the given problem. For our data structure we then need to apply functions that impose that invariant on that given data structure.

For example, if our input is a set, and we want to impose permutation invariance, some common functions are `add`, `avg`, or `max`. All of those functions impose permutation invariance on sets, and have different trade offs dependent on the downstream task.

These however are not the optimal functions as they are lossy. Summing up all the elements of a set is permutation invariant, but you lose a lot of information about your set: you collapse it into a single statistic!

The goal of this post is to list invariant imposing *lossless* (or near lossless) functions for most common data structures.

List of data structures:
- Sets
- Graphs
- Time series
- distribution 

List of common transformations that we want to be invariant to
- Permutation
- Translation (= shift ?)
- Rotation
	- $x = x*e^{i\theta}$ (?)
- reflection
- Dilation
- Scaling
- Order
	- If $x < y \implies f(x) < f(y)$
- Metric
	- $|x-y|=|f(x)-f(y)|$
- Addition (another term exists )
	- $f(x+y)=f(x)+f(y)$




Set:
- Permutation — sorting, distribution approximation 

Examples:
- Circulant Matrices are shift-invariant [HN Comment](https://news.ycombinator.com/item?id=37915848)

Invariant Functions
- Shift Invariant 
	- Fourier Transform
- Scale Invariant 
	- Mellin Transform