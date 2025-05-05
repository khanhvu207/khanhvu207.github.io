---
layout: posts
title: Estimating density ratios without knowing the densities
usekatex: true
published: true
comments: true
---

Suppose we have two probability distributions $p$ and $q$ over the same space $\mathcal{X}$, and we want to estimate the density ratio $\frac{q(x)}{p(x)}$ for some $x \in \mathcal{X}$.
The caveat is that we do not know the densities $p$ and $q$, but we have a way to sample from them.
I came across a beautiful idea for this while reading [Variational Inference using Implicit Distributions](https://arxiv.org/pdf/1702.08235) by [Ferenc Huszár](https://www.inference.vc/about/): We can estimate density ratios via a discriminator that learns to distinguish between samples from $p$ and $q$.

Let's label the samples from $q$ as $1$ and the samples from $p$ as $0$.
We can then train a discriminator $D$ (parameterized by a neural net) to predict the label of a sample $x$, in particular, we want to model $D(x) = \Pr(y=1|x)$.
The training objective is to minimize the binary cross-entropy loss:

$$
\mathcal{L}(D) = -\mathbb{E}_{x \sim p}[\log(1 - D(x))] - \mathbb{E}_{x \sim q}[\log(D(x))].
$$

Solving for $\frac{\partial \mathcal{L}}{\partial D} = 0$ (you can double-check that $\mathcal{L}(D)$ is convex), we get the optimal discriminator:

$$
D^*(x) = \frac{q(x)}{p(x) + q(x)}.
$$

This in fact coincides with the posterior $\Pr(y=1\mid x)$ obtained from Bayes' theorem.
Now, we just compute the odds ratio to get the density ratio:

$$
    \frac{D^*(x)}{1 - D^*(x)} = \frac{q(x)}{p(x)}. \quad\square
$$

Voila! 🎉

Regarding the motivation for why we want to estimate density ratios, it is often used in variational inference with implicit distributions.
The evidence lower bound (ELBO) contains a Kullback-Leibler divergence term between the variational distribution $q_\phi$ and the prior $p$:

$$
\mathbb{D}_{\text{KL}}[q_\phi \| p] = \mathbb{E}_{x \sim q_\phi}\left[\log \frac{q_\phi(x)}{p(x)}\right].
$$

If we liberate ourself from using a tractable density for modeling $q_\phi$, we can use the above method to estimate the density ratio $\frac{q_\phi(x)}{p(x)}$ without even knowing what the actual density $q_\phi$ is.
