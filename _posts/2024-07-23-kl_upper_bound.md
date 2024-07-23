---
layout: posts
title: An upper bound for the Kullback-Leibler divergence 
usemathjax: true
published: true
---

Zürich is so hot now in the night that I couldn't fall asleep.
While lying on the bed, I arrived at an interesting upper bound for the [KL divergence (relative entropy)](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) between two Bernoulli distributions.
In this case, the KL divergence is expressed as:

$$
    D_\text{KL}(p \parallel q) := p\log\left(\frac{p}{q}\right) + (1-p)\log\left(\frac{1-p}{1-q}\right)
$$

Here, $$p, q$$ denoting the parameters of the two distributions.
I assume $$p, q \in (0, 1)$$ and $$\log(\cdot)$$ is the natural logarithm function.
For other cases like when $$p>0$$ and $$q=0$$, the KL divergence diverges to infinity.
I can show that, in this setting, the following holds:

$$
    D_\text{KL}(p \parallel q) \le \frac{(p - q)^2}{(1-q)q} =: \frac{2D_\text{TV}(p, q)^2}{\mathbb{V}[X]}
$$

Above, $$D_\text{TV}(\cdot, \cdot)$$ is the [variational distance](https://en.wikipedia.org/wiki/Total_variation_distance_of_probability_measures) between two distributions and $$X$$ is a random variable distributed according to Bernoulli($$q$$).
My result is in fact a looser bound as compared to the [reversed Pinsker's inequality](https://en.wikipedia.org/wiki/Pinsker%27s_inequality) which replaces the denominator with $$\min \{q, 1-q\}$$.

For the proof, I define a function $$f(q):=D_\text{KL}(p \parallel q)$$ with $$p$$ kept as a constant.

**Corollary 1.**
The function $$f$$ is convex on $$(0, 1)$$.

_Proof_.
$$f$$ is convex if and only if $$\forall \lambda \in [0, 1]$$ and $$\forall q_1, q_2 \in \text{dom}(f)$$:

$$
    f(\lambda q_1 + (1-\lambda) q_2) \le \lambda f(q_1) + (1-\lambda) f(q_2)
$$

Indeed this is true since:

$$
\begin{align}
    f(\lambda q_1 + (1-\lambda) q_2) &= p \log\left(\frac{p}{\lambda q_1 + (1-\lambda) q_2}\right) + (1-p) \log\left(\frac{1-p}{1 - \lambda q_1 - (1-\lambda) q_2}\right) \\
    &= -p \log\left(\frac{\lambda q_1 + (1-\lambda) q_2}{p}\right) - (1-p) \log\left(\frac{\lambda (1-q_1) + (1-\lambda) (1-q_2)}{1-p}\right) \\ 
    &\le \lambda \left(p\log\frac{p}{q_1} + (1-p)\log\frac{1-p}{1-q_1} \right) + (1-\lambda) \left(p\log\frac{p}{q_2} + (1-p)\log\frac{1-p}{1-q_2} \right) \\
    &= \lambda f(q_1) + (1-\lambda) f(q_2)
\end{align}
$$

**Corallary 2.**
$$\forall q_1, q_2 \in \text{dom}(f)$$, it holds that:

$$
    f(q_1) \ge f(q_2) + f'(q_2)(q_1 - q_2)
$$

where $$f'$$ is the first-order derivative of $$f$$ at $$q_2$$.

_Proof_.
Given that $$f$$ is convex on its domain, we have:

$$
\begin{align}
    f(\lambda q_1 + (1-\lambda) q_2) &\le \lambda f(q_1) + (1-\lambda) f(q_2) \\
    f(q_2 + \lambda(q_1 - q_2)) - f(q_2) + \lambda f(q_2) &\le \lambda f(q_1) \\
    \frac{f(q_2 + \lambda(q_1 - q_2)) - f(q_2)}{\lambda (q_1 - q_2)} (q_1 - q_2) + f(q_2) &\le f(q_1) &\text{(Dividing $\lambda$ both sides)} \\
    \lim_{\lambda \rightarrow 0} \frac{f(q_2 + \lambda(q_1 - q_2)) - f(q_2)}{\lambda (q_1 - q_2)} (q_1 - q_2) + f(q_2) &\le f(q_1) \\
    f'(q_2) (q_1 - q_2) + f(q_2) &\le f(q_1)
\end{align}
$$

We can explicitly derive the derivative of $$f$$ at a given $$q$$:

$$
    f'(q) = \frac{p - q}{(q - 1)q}
$$

Putting everything together, we have the desired upper bound:

$$
\begin{align}
    f(p) &\ge f(q) + f'(q)(p - q) &\text{(Corollary 2)}\\
    f'(q)(p - q) &\ge f(q) &\text{($f(p)=0$ by definition)}\\
    \frac{(q - p)^2}{(1 - q)q} &\ge f(q):= D_\text{KL}(p \parallel q)
\end{align}
$$

This bound is particularly useful when it comes to deriving some concentration bounds on the KL divergence with respect to $$q$$.
I will discuss this matter in another blog post.
It's 1:44AM now and I need to go to bed for real this time.