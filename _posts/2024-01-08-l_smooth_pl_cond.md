---
layout: post
title: L-smoothness and the Polyak-Lojasiewicz condition
usemathjax: true
published: false
---

When doing gradient descent, we often need to make regularity assumptions about the objective function, $$H(\theta)$$, to ensure the algorithm can converge to the global minimum with a reasonable rate.
Two conditions, *L-smoothness* and *Polyak-Łojasiewicz (PL)*, play complemantary roles in the picture.

<u>Definition 1:</u>
$$H(\theta)$$ is L-smooth if it satisfies $$\forall \theta, \theta'$$,

$$
\|\nabla H(\theta) - \nabla H(\theta')\| \leq L \|\theta - \theta'\|
$$

This definition is analogous to considering the Lipschitz continuity of the gradient map of $$H(\theta)$$.
Intuitively, the smoothness condition ensures that the gradient should not change much when moving from a point to a neighboring point in the domain.

<u>Definition 2:</u>
A twice continuously differentiable function $$H(\theta)$$ is *strongly convex* if the Hessian $$\nabla^2 H(\theta) \succeq \mu I$$ for all $$\theta$$ and some $$\mu>0$$.
As a consequence, considering the second-order Taylor's approximation of $$H(\theta)$$, a strongly convex function $$H(\theta)$$ is lower bounded by a quadratic form:

$$
\begin{align}
H(\theta') &= H(\theta) + (\theta' - \theta)^\top \nabla H(\theta) + \frac{1}{2}(\theta' - \theta)^\top \nabla^2 H(\theta) (\theta' - \theta)\\
&\geq H(\theta) + (\theta' - \theta)^\top \nabla H(\theta) + \frac{\mu}{2} \| \theta'-\theta \|^2
\end{align}
$$

<center>
    <figure>
        <img src="" alt="l-smooth_strong-convexity" style="width:500px; display: inline-block;"/>
        <figcaption>...</figcaption>
    </figure>
</center>

<u>Definition 3:</u>
$$H(\theta)$$ satisfies the Polyak-Łojasiewicz (PL) condition if $$\forall \theta$$ we have

$$
H(\theta) - \min H \leq \frac{1}{2\mu} \| \nabla H(\theta) \|^2
$$

PL condition is a weaker condition compared to strong convexity, but it usually is sufficient for ensuring the desire rate of convergence.
In contrast to the smoothness condition, PL condition tells us that the function $$H(\theta)$$ should not generally be too flat, with the exception around the global minimum.

