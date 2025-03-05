---
layout: posts
title: Confidence sets under asymptotic normality assumption
usemathjax: true
published: true
---

In statistical inference, we often want to construct confidence sets for the parameter of interest; for example, you may have heard of the [95% confidence interval](https://en.wikipedia.org/wiki/Confidence_interval).
Confidence sets are a way to quantify the uncertainty in our estimates.
At its core, the construction of confidence sets is typically achieved through inverting some concentration inequalities.
We shall explore this in a setting where the estimator is [asymptotically normal](https://en.wikipedia.org/wiki/Asymptotic_distribution#Central_limit_theorem) under some regularity conditions.

## Linear regression with fixed design

**The setting.**
We observe $$n$$ samples $$\{(Y_t, x_t)\}_{t=1}^n$$ that follows the linear regression model

$$
    Y_t = \langle x_t, \theta_\star \rangle + \eta_t,
$$

where the design vector $$x_t \in \mathbb{R}^d$$ is fixed and known, the noise $$\eta_t$$ is drawn i.i.d from a Gaussian $$\mathcal{N}(0, \sigma^2)$$ and the parameter of interest is $$\theta_\star \in \mathbb{R}^d$$.
For simplicity, we assume that the Gram matrix $$V:= X^\top X = \sum_{t=1}^n x_t x_t^\top$$ is non-singular (here, $$X = [x_1^\top, \ldots, x_n^\top]^\top$$).

Our goal is to construct a confidence set $$C_n$$ such that $$\Pr(\theta_\star \in C_n) \geq 1 - \delta$$ for some confidence level $$\delta \in (0, 1)$$.
We will start with the maximum-likelihood estimator and then derive the asymptotic distribution of the estimator.
Under the above assumptions, the maximum-likelihood estimator $$\hat{\theta}$$ is given by

$$
    \hat{\theta} = V^{-1} X^\top Y,
$$

which is also known to be [asymptotically normal](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation#Consistency).
More precisely, the distribution of $$\hat{\theta}$$ converges to a Gaussian distribution when $$n$$ is large, i.e.,

$$
    \hat{\theta} \approx \mathcal{N}(\theta_\star, n^{-1}\mathcal{I}(\theta_\star)^{-1}).
$$

Above, $$\mathcal{I}(\theta_\star)$$ is the Fisher information matrix at $$\theta_\star$$.
It is also known that $$\hat{\theta}$$ is [asymptotically efficient](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation#Efficiency), i.e., it achieves the [Cramér-Rao lower bound](https://en.wikipedia.org/wiki/Cram%C3%A9r%E2%80%93Rao_bound)

$$
    \mathbb{V}(\hat{\theta}) = \mathcal{I}(\theta_\star)^{-1}
$$

This is a bless because it allows us to work out the formula for the inversed Fisher information matrix:

$$
\begin{align*}
    \mathbb{V}(\hat{\theta}) &= \mathbb{V}(V^{-1} X^\top Y) \\
    &= \mathbb{V}(V^{-1} X^\top (X \theta_\star + \eta)) \\
    &= V^{-1} X^\top \mathbb{V}(\eta) X V^{-1} \\
    &= \sigma^2 V^{-1} X^\top X V^{-1} \\
    &= \sigma^2 V^{-1}.
\end{align*}
$$

From this we then have that:

$$
\begin{align*}
    \hat{\theta} &\approx \mathcal{N}(\theta_\star, \sigma^2 n^{-1} V^{-1}) \\
    \frac{\sqrt{n}}{\sigma} V^{1/2}(\hat{\theta} - \theta_\star) &\approx \mathcal{N}(0, I_d)
\end{align*}
$$

Let $$Z := \frac{\sqrt{n}}{\sigma} V^{1/2}(\hat{\theta} - \theta_\star)$$, then we have $$\|Z\|_2^2 = \frac{n}{\sigma^2} \|\hat{\theta}-\theta_\star\|_{V}^2$$ follows a $$\mathcal{X}_d^2$$-distribution with $$d$$ degrees of freedom.
From [the tail bounds of the $$\chi^2$$-distribution](https://stats.stackexchange.com/a/4821/301376), we have

$$
\begin{align*}
    \Pr\left(\|Z\|_2^2 \geq d + 2\sqrt{d\log(1/\delta)} + 2\log(1/\delta)\right) &\leq \delta \\
    \Pr\left(\|\hat{\theta}-\theta_\star\|_{V}^2 \geq \frac{\sigma^2}{n} \left(d + 2\sqrt{d\log(1/\delta)} + 2\log(1/\delta)\right)\right) &\leq \delta.
\end{align*}
$$

So if we define the confidence set $$C_n$$ as

$$
    C_n = \left\{\theta \in \mathbb{R}^d: \|\hat{\theta}-\theta\|_{V}^2 \leq \frac{\sigma^2}{n} \left(d + 2\sqrt{d\log(1/\delta)} + 2\log(1/\delta)\right)\right\},
$$

then it is a $$(1-\delta)$$-confidence set for $$\theta_\star$$.
