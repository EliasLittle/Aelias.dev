---
title: "Proposal for a System 2 Model"
date: "2025-02-21"
draft: true
showToc: true
TocOpen: false
---
## Introduction

I recently read this<cite>[^1]</cite> tweet, which referenced the LLaDa paper ([Large Language Diffusion Models](https://arxiv.org/pdf/2502.09992)). And with Inception's recent [announcement of their Mercury models](https://www.inceptionlabs.ai/news), I've been thinking a lot about Diffusion architectures for large language models, and I'm really excited about them. I harbor very similar sentiments that diffusion feels far more analogous to how humans think compared to the transformer-based token autoregressive models (ARMs) that currently dominate the field.

Instead of simply predicting what the next token should be, us humans ideate some concept as a whole and then put it into words (or some other medium). Using a diffusion model is much closer to this as it effectively generates all the tokens together in parallel instead of one at a time. 

### System 1 Thinking 

Under System 1 thinking<cite>[^2]</cite> we effectively do the same thing as the diffusion models, directly spitting out an answer or response to something: words-in directly to words-out; fast. Most small talk, and simple conversations are exactly like this, you don’t need to think much or at all, and yet conversation moves freely. 

But note that currently ARMs are quite good at system 1 style thinking, even though the primary idea behind it is very different from how we actually think. But the hypothesis that I believe (and it appears Inception does as well) is that a Diffusion architecture will actually be better (i.e. faster, cheaper) than ARMs. 

But I think Diffusion models can help lay the groundwork for not just cheaper models with the same capabilities, but for more complicated models with more adept reasoning than any present models.

### System 2 Thinking

Many attempts have been made at more complicated System 2 thinking<cite>[^2]</cite>, but most of these are simply longer (sometimes hidden) chains of System 1 thinking, and maybe combined with some tool use to bring in new information. This is definitely improved, both empirically and subjectively<cite>[^3]</cite>. However, it still doesn’t exactly map to how I think of my own System 2 thinking. 

When I’m thinking hard or deeply about something, it’s often fuzzy and definitely not a nice orderly string of structured sentences. Often times I’m have a clear idea in my head, but then have to spend sometime to translate that into words (i.e. “give me a second to find the words for it”). Other times I’ll simply have some picture in my head, especially for spatial reasoning tasks, navigating, or sometimes organization. This type of thinking is absolutely not included in the long chain-of-text style that LLMs currently use. 

Likewise, I can take the same idea in my head and present it in many different ways. Most common are via language, either written or spoken, but other times it might been sketching something out, humming a melody, mocking up a 3d model, all from a single fuzzy idea in my head. 

This is key point 1
> Deep thought is often done with broad concepts, and not individual tokens

But individual concepts on their own, while useful, don't fully constitute system 2 thinking. These concepts must evolve; this is where ARM's shine. They do a phenomenal job capturing relationships between embeddings and which embeddings tend to follow others. 

This is key point 2
> Attention based ARMs are really strong at capturing relationships between embeddings

## Proposal

Lets tackle this in a few parts:
1. We want to take advantage of the fast parallel generation from diffusion models 
2. We want to operate on more generalized concepts instead of small text embeddings
3. We want to utilize ARMs ability to capture ordered relationships between embeddings



Currently Diffusion based models all "diffuse" from some noisy distribution directly to the target medium (directly to pixels, or tokens). Instead I propose having a diffusion model generate latent space "concepts" which can then be “rendered” into any given medium (text, images, audio, etc...). This gives a slightly more complicated multi-modal model, but still not system-2 type thinking. To get system-2 style thinking we want to be able to iterate on these concepts. This means we want concepts to be able to interact with each other either like token embeddings in ARMs. 

This combines diffusion's direct generation with the iteration of AR models. 






⊹

[^1]:  Tweet:
	{{< tweet user="AlpinDale" id="1891346808128823413" >}}

[^2]: From Thinking Fast and Slow by Daniel Kahneman ([Wikipedia](https://en.wikipedia.org/wiki/Thinking,_Fast_and_Slow?wprov=sfti1))

[^3]: Well, at least in my mind 😜
