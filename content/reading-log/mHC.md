---
title: "mHC: Manifold-Constrained Hyper-Connections"
date: 2026-01-09
tags:
  - ml
  - papers
draft: false
---

[ArXiV](https://www.arxiv.org/abs/2512.24880) and excellent [video explainer](https://www.youtube.com/watch?v=jYn_1PpRzxI) from Jia-Bin Huang.

### Summary:
This paper extends on the [[Hyper-Connections|Hyper Connections]] paper. Hyper connections were introduced as a way to essentially apply the idea of a mixture of experts (MoE) to residual connections. Instead of having a single residual connection, let's have multiple different residual connections each with a linear mapping to "extract" different parts of the main layer.

Manifold-Constrained Hyper Connections improves upon this by improving training stability. They do this by constraining the linear layer applied to the residual connection "heads" to a doubly stochastic matrix that way all values sum to 1 which should prevent exploding or vanishing gradients. 

Because there is this linear term applied to each residual connection, after $n$ layers this term gets multiplied $n$ times, so if the term was greater than 1 then it explodes, and if its less then it vanishes. Constraining these values to the manifold of doubly-stochastic matrices forces all of them to be near 1 and thus keep the residual connection stable.

They also updated the parameterization of the added layers by adding sigmoids to help keep the other layers constrained to 1 as well.

Finally, the also introduced some infrastructure improvements to help with the increased memory footprint of hyper connections. These include fused operations with shared memory access, recomputing intermediate activations rather than storing them in memory, and improved pipeline parallelism.

### Thoughts:

mHC is an effective improvement to hyper connections, and is bringing much more attention both to the importance of training stability, and to residual connections. 

This has definitely made me think much more about residual connections, but has actually made me question them a little bit. I'm curious if they're the right strategy in the long term. They make learning easier and smoother, but seems like at a cost of size and expressibility. If we're forced to always learn updates to add to values rather than learning much more expressive transformations of the original values themselves, then it seems like we might need many more layers of updates in order to achieve the same expressive power. It feels like a tradeoff of needing more layers and more weights in order to achieve the same transformations, but in exchange for being easier to train the values to match our loss functions.

Makes me wonder if we might be able to get just as powerful models in a smaller package if have a better method of training and omit residual connections. Just some musings though!