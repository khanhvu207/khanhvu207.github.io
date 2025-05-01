---
layout: posts
title: Counting with expectation
usemathjax: true
published: true
---


**Problem 10 (Individual test, ARML 2013)**.
For a positive integer $$n$$, let $$C(n)$$ equal the number of pairs of consecutive $$1$$'s in the binary representation of $$n$$.
For example, $$C(183)=C(10110111_2)=3$$.
Compute $$C(1) + C(2) + \ldots + C(256)$$.

*Solution*.
You can go ahead with doing casework and will eventually discover some recurrent formulas like in the [intended solution](https://www.arml.com/ARML/arml_2019/public_contest_files/2009_2014_book/ARML_2009_2014.pdf).

However, this problem has a shorter solution that leverages [linearity of expectation](https://en.wikipedia.org/wiki/Expected_value#Properties:~:text=0.-,Linearity%20of%20expectation,-%3A%5B34).
In short, linearity of expectation means:

$$
\mathbb{E}[\mathrm{X}_1 + \mathrm{X}_2 + \ldots + \mathrm{X}_n] = \mathbb{E}[\mathrm{X}_1] + \mathbb{E}[\mathrm{X}_2] + \ldots + \mathbb{E}[\mathrm{X}_n]
$$

This holds regardless of whether these random variables are dependent or not.
Now, we can rewrite the target quantity in term of an expectation:

$$
    \sum_{i=1}^{256} C(i) = 256 \cdot \mathbb{E}[C(\mathrm{X})],
$$

for $$\mathrm{X}$$ being a discrete RV drawn uniformly from $$\{1, 2, \ldots, 256\}$$.
Let $$\text{bin}(\mathrm{X})$$ denotes the binary representation of $$\mathrm{X}$$ and $$\text{bin}(\mathrm{X})_i$$ denotes the $$i$$-th bit from the left.
Let $$Z_i = [\text{bin}(\mathrm{X})_i = 1 \land \text{bin}(\mathrm{X})_{i+1} = 1]$$ be an indicator function that takes value 1 if both $$i$$-th and $$(i+1)$$-th bits are $$1$$'s, and otherwise takes value 0.
Given this, we can write

$$
\begin{align}
\mathbb{E}[C(\mathrm{X})] &= \mathbb{E}\left[\sum_{i=1}^7 Z_i\right] \\
&= \sum_{i=1}^7 \mathbb{E}[Z_i] &\text{(Linearity of expectation)}\\ 
&= \sum_{i=1}^7 \mathbb{P}\left(\text{bin}(\mathrm{X})_i = 1 \land \text{bin}(\mathrm{X})_{i+1} = 1\right)\\
&= 7\cdot \frac{1}{2^2} = \frac{7}{4}
\end{align}
$$

The answer would then be $$256 \cdot \frac{7}{4} =\boxed{448}$$.
Cute trick isn't it?