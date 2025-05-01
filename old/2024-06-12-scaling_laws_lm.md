---
layout: posts
title: Neural scaling laws
usemathjax: true
published: false
---

<!-- *"Nature must be interpreted as matter, energy, and information"* (Jeremy Campbell, Grammatical Man) -->

Performance of deep learning models is seemingly predictable[^1].
Empirically, the loss scales as a power-law of the model size, the dataset size and the amount of compute [^2].
This is a remarkable finding, but to some extent not so surprising[^3].

In this blog post, I will summarize the key findings of the previous work and discuss the implications of the scaling laws.
The main emphasis will be on the scaling with dataset size because I think it is the most interesting and least understood aspect of the scaling laws.

## References

[^1]: [Hestness, Joel, et al. ‘Deep Learning Scaling Is Predictable, Empirically’. CoRR, vol. abs/1712.00409, 2017](http://arxiv.org/abs/1712.00409)
[^2]:
[^3]: [Scaling laws of recovering Bernoulli](https://kyunghyuncho.me/scaling-law-of-estimating-bernoulli/)