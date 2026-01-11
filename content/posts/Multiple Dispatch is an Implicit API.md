---
title: Multiple Dispatch is an Implicit API
date: 2025-02-21
draft: true
showToc: true
TocOpen: false
---

When you call a function like `add(x,y)` you’re not actually calling a specific method, you’re calling an API.

This can help with many things including 
* Dispatching for different types: int vs float vs matrix
* Backend independent: run on nvidia vs amd 
* Versioning: update the implementation without updating downstream uses

If we look at traditional APIs like REST for example, 
