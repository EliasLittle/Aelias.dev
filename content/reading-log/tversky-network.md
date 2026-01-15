---
title: Tversky Neural Networks
tags:
  - ml
  - papers
draft: false
---
[ArXiV](https://arxiv.org/abs/2506.11035)

### Summary

Amos Tversky's seminal paper [Features of Similarity](https://pages.ucsd.edu/~scoulson/203/tversky-features.pdf), introduced a similarity metric for how humans perceive similarity. Tversky's similarity metric is non-symetric as human preferences empirically are. By using this non-symmetric metric in neural networks, its able to more-easily learn non-linear functions such as `XOR`. 

The primary similarity function they introduce is:
$$
S(a,b) = \theta f(A\cap B) - \alpha f(A - B) - \beta f(B - A)
$$

There are several parts to this.

**Salience**: The relative salience  (prominence of their features) determines the direction of asymmetry of similarity. Less salient features are assessed to be more similar to more salient features than vice-versa.

$$
\text{Salience}(A) = f(A) = \sum_{k=1}^{|\Omega|}a\cdot f_k \cdot \mathbb{1}[a\cdot f_k > 0] 
$$

**Feature Set Intersection**: 
$$
f(A\cap B) = \sum_{k=1}^{|\Omega|} \Psi(a\cdot f_k, b\cdot f_k) \times \mathbb{1}[a\cdot f_k > 0 \wedge b\cdot f_k > 0]
$$
**Feature Set Difference**:
They introduce two different versions, one where they measure if features are present in A but not in B, and an alternative version that includes features that are present in both, but a greater amount in A compared to B.

$f^i$: Features in A but not in B
$f^s$: Accounts for features in both, but in greater amount in A
$$
f^i(A - B) = \sum_{k=1}^{|\Omega|}(a\cdot f_k) \times \mathbb{1}[a\cdot f_k > 0 \wedge b\cdot f_k > 0]
$$
$$
f^s(A-B) = f^i(A-B) + \sum_{k=1}^{|\Omega|}(a\cdot f_k - b \cdot f_k) \times \mathbb{1}[b\cdot f_k > 0 \wedge a\cdot f_k > b\cdot f_k]
$$

#### Project Layer
Using this similarity function, they then introduce a projection layer where they introduce $p$ "prototypes" $\Pi_i \in \mathbb{R}^d$ and then calculate the similarity between a given vector $a\in\mathbb{R}^d$ and each prototype $S(a,\Pi_i)$ resulting in a vector $p(a) \in\mathbb{R}^p$ thus project our vector $a$ from $\mathbb{R}^d$ to $\mathbb{R}^p$.

#### Dual Representation of Objects
One of their key points is representing objects both as vectors and sets.

Given a learnable finite universe $\Omega$ of feature vectors $f_k\in\mathbb{R}^d$ and an object represented as the vector $x\in\mathbb{R}^d$, they propose $x\dot f_k$ to be the scalar measure of feature $f_k$ in $x$ and a second representation of $x$ as the set $X=\{f_k\in\Omega| x\cdot f_k > 0\}$  of features with which $x$ has a positive dot product.

### Thoughts

I think this is a pretty unique idea, normally researchers are looking to make things asymmetric functions symmetric, not the other way around. But if we want to make LLMs more human-like in their thinking and understanding then this is very likely a path we'll want to explore further. 

That being said, I'm not convinced whether or not we actually want this type of asymmetry. While I definitely think it'd be useful in making LLM more human like, I'm not sure this is a desirable behavior of human thought. Having this sort of asymmetry actually makes it much more difficult to reason with humans as their preferences or perceptions can be illogical due to this. 

It might be useful to include this type of asymmetry when ingesting training data as it might better be able to capture some of the meaning of human written text and thoughts, but not when generating text.

If we picture a basic encoder-decoder model, instead of including baking this similarity metric into both, I think it should instead just be a precursor to the encoder, and the decoder should never be aware of it.

Curious to see if any production models ever end up using an asymmetric similarity metric like this.

