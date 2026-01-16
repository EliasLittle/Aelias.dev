---
title: Stacked Diffs Versus Pull Requests
tags:
  - git
  - jj
draft: true
---
I've been learning [Jujutsu](https://docs.jj-vcs.dev/latest/) (`jj`) and the tutorial mentioned that they (and LLVM) prefer and recommend using "Stacked Diffs" and linked this blog post explaining them.

[Stacked Diffs Versus Pull Requests](https://jg.gg/2018/09/29/stacked-diffs-versus-pull-requests)

I've worked with Github's "standard" Pull Request process as described in the blog, I've also worked with a different custom system called Iron briefly during my time at Jane Street[^1].  Each have their pros and cons, but its also very clear that they each had very different priorities when they were being designed. 

One of the main points that the post points out is that the standard pull request workflow often ends up with lots of junk commits that are just small progress checks or addressing feedback with commit messages like "fixes". This often means that the commit history is nearly meaningless. From my (albeit limited) experience this is absolutely true, and one of the biggest drawbacks from the pull request workflow.

[^1]: There's a great talk about it [here](https://www.janestreet.com/tech-talks/janestreet-code-review/) by [Ian Henry](https://ianthehenry.com/).
