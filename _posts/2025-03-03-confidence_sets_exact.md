---
layout: posts
title: Time-uniform confidence sets for Gaussian linear models
usemathjax: true
published: true
---

In statistical inference, we often want to construct confidence sets for the parameter of interest; for example, you may have heard of the [95% confidence interval](https://en.wikipedia.org/wiki/Confidence_interval).
Confidence sets are a way to quantify the uncertainty in our estimates.
At its core, the construction of confidence sets is typically achieved through inverting some concentration inequalities.
<!-- We shall explore this in a setting where the estimator is [asymptotically normal](https://en.wikipedia.org/wiki/Asymptotic_distribution#Central_limit_theorem) under some regularity conditions. -->

**The setting.**
We observe $$n$$ samples $$\{(Y_t, x_t)\}_{t=1}^n$$ that follows the linear regression model

$$
    Y_t = \langle x_t, \theta_\star \rangle + \eta_t,
$$

where the design vector $$x_t \in \mathbb{R}^d$$ is fixed and known, the noise $$\eta_t$$ is drawn i.i.d from a Gaussian $$\mathcal{N}(0, 1)$$ and the parameter of interest is $$\theta_\star \in \mathbb{R}^d$$.
For simplicity, we assume that the Gram matrix $$V:= X^\top X = \sum_{t=1}^n x_t x_t^\top$$ is non-singular (here, $$X = [x_1^\top, \ldots, x_n^\top]^\top$$).
For brevity, we can write $Y = X\theta_\star + \eta$ with $Y\in \mathbb{R}^n$ and $\eta \in \mathbb{R}^n$.

Our goal is to construct a sequence of confidence sets $$C_1, C_2, \ldots$$ such that $$\Pr(\exists n \ge 1: \theta_\star \notin C_n) \le \delta$$ for some confidence level $$\delta \in (0, 1)$$.
We will start with the maximum-likelihood estimator and then construct the confidence set for a certain $n$. 
Under the above assumptions, the maximum-likelihood estimator $$\hat{\theta}$$ is given by

$$
    \hat{\theta} = V^{-1} X^\top Y,
$$

which turns out to be a Gaussian because $Y$ is a Gaussian vector as given. 
In particular, we can write

<!-- which is also known to be [asymptotically normal](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation#Consistency) (well, in fact $$\hat{\theta}$$ is normal already due to the Gaussian noise!). -->

<!-- More precisely, the distribution of $$\hat{\theta}$$ converges to a Gaussian distribution when $$n$$ is large, i.e.,

$$
    \hat{\theta} \sim \mathcal{N}(\theta_\star, n^{-1}\mathcal{I}(\theta_\star)^{-1}).
$$

Above, $$\mathcal{I}(\theta_\star)$$ is the Fisher information matrix at $$\theta_\star$$.
It is also known that $$\hat{\theta}$$ is [asymptotically efficient](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation#Efficiency), i.e., it achieves the [Cramér-Rao lower bound](https://en.wikipedia.org/wiki/Cram%C3%A9r%E2%80%93Rao_bound)

$$
    \mathbb{V}(\hat{\theta}) = n^{-1} \mathcal{I}(\theta_\star)^{-1}
$$

This is a bless because it allows us to work out the formula for the inversed Fisher information matrix:

$$
\begin{align*}
    \mathbb{V}(\hat{\theta}) &= \mathbb{V}(V^{-1} X^\top Y) \\
    &= \mathbb{V}(V^{-1} X^\top (X \theta_\star + \eta)) \\
    &= V^{-1} X^\top \mathbb{V}(\eta) X V^{-1} \\
    &=  V^{-1} X^\top X V^{-1} \\
    &=  V^{-1}.
\end{align*}
$$

From this we then have that: -->

$$
\begin{align*}
    \hat{\theta} &\sim \mathcal{N}(\theta_\star,  V^{-1}) \\
     V^{1/2}(\hat{\theta} - \theta_\star) &\sim \mathcal{N}(0, I_d)
\end{align*}
$$

Let $$Z :=  V^{1/2}(\hat{\theta} - \theta_\star)$$, then we have $$\|Z\|_2^2 =  \|\hat{\theta}-\theta_\star\|_{V}^2$$ follows a $$\mathcal{X}_d^2$$-distribution with $$d$$ degrees of freedom.
From [the tail bounds of the $$\chi^2$$-distribution](https://stats.stackexchange.com/a/4821/301376), we have

$$
\begin{align*}
    \Pr\left(\|Z\|_2^2 \geq d + 2\sqrt{d\log(1/\delta)} + 2\log(1/\delta)\right) &\leq \delta
\end{align*}
$$

So if we define the confidence set $$C_n$$ as

$$
    C_n = \left\{\theta \in \mathbb{R}^d: \|\hat{\theta}-\theta\|_{V}^2 \leq  d + 2\sqrt{d\log(1/\delta)} + 2\log(1/\delta)\right\},
$$

then it is a $$(1-\delta)$$-confidence set for $$\theta_\star$$.

Using union bound, we can construct a sequence of confidence sets $$C_1, C_2, \ldots$$ such that $$\Pr(\exists n \ge 1: \theta_\star \notin C_n) \leq \delta$$ by choosing a larger confidence set for each $$n$$:

$$
    C_n = \left\{\theta \in \mathbb{R}^d: \|\hat{\theta}-\theta\|_{V}^2 \leq  d + 2\sqrt{d\log(n(n+1)/\delta)} + 2\log(n(n+1)/\delta)\right\}.
$$

Geometrically, the confidence set $$C_n$$ is an ellipsoid centered at $$\hat{\theta}$$ with the principal axes aligned with the eigenvectors of the Gram matrix $$V$$.
The length of the axes is determined by the eigenvalues of the Gram matrix $$V$$ and the confidence level $$\delta$$.
To see this, for any confidence set that is of the form $$C_n = \{\theta :\|\hat{\theta} - \theta\|_V^2 \le \beta\}$$, we can rewrite it as

$$
    C_n = \hat{\theta} + \beta^{1/2} V^{-1/2} \mathcal{B}_d,
$$

where $$\mathcal{B}_d$$ is the unit $$\ell_2$$-ball in $$\mathbb{R}^d$$.
Furthermore, the volume of this ellipsoid is exactly the determinant of $$\beta^{1/2} V^{-1/2}$$.
The calculation of this determinant is a bit dense but it invokes the _Elliptical potential lemma_ (see Lemma 19.4 in [Bandit Algorithms](https://tor-lattimore.com/downloads/book/book.pdf)) which turns out to scale as $$\mathcal{O}\left(\frac{\log n}{n}\right)^{d/2}$$.
This basically agrees with the classical concentration rate $$\mathcal{O}(1/\sqrt{n})$$ from the [Hoeffding's inequality](https://en.wikipedia.org/wiki/Hoeffding%27s_inequality) in the 1-dimensional case, where the extra $$\log n$$ factor arises due to the time-uniform guarantee.
