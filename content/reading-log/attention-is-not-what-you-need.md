---
title: Attention is Not What You Need
date: 2026-01-02
tags:
  - ml
  - papers
  - attention
draft: false
---

[Original Paper](https://arxiv.org/abs/2512.19428)

### Summary:
This paper views attention as:
> a particular instance of _tensor lifting_: a hidden vector is mapped into a high-dimensional space of pairwise interactions, and learning proceeds by constraining this lifted tensor through gradient descent. 

As an alternative, they propose "an _attention-free_ sequence model built around _Grassmann flows_." 

Instead of lifting from token space to pairwise interaction space, consider lifting to a Grassman manifold. The hidden states are points on this manifold. Each forward pass traces a path on this manifold. This path is a flow that we can learn.

Given vectors $Z_t$ and $Z_{t+\Delta}$, consider the 2D subspace spanned by these vectors. Then project back to the model dimension and aggregate (avg.) across offsets $\Delta$. Following this, concatenate the original hidden state and the "Grassman feature" and "compute a gate" (apply linear layer) followed by layer norm and dropout.
### Thoughts:
To me, this is one of the most exciting papers in recent times. It relates very closely to a lot of the ideas I've been floating around regarding using multivectors and geometric algebra instead of being constrained to simple vectors as is standard practice.

In a (_very_) abstract way, I think conceptualizing language as a flow/path in the space of token pairs also lends itself slightly to the classic metaphor: "The flow of conversation"

I would be very curious to see this idea expanded to higher levels of abstraction beyond token pairs. I think it would be very interesting to try and model the flow of a paragraph in sentence pair space for example compared to the flow of a sentence in token pair space.

Maybe these could both be modeled as a single flow in a higher dimensional space since if a sentence is the flow of tokens, and a paragraph is the flow of sentences then really a paragraph is also a flow of tokens, but it'd be useful to "visualize" or operate at different granularities of this flow[^1].

Looking forward to other research in this area, and seeing what other people might do with it.

[^1]: This actually reminds me a little of the [Coastline Paradox](https://en.wikipedia.org/wiki/Coastline_paradox), though I don't think its particularly useful here.
