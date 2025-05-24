---
layout: posts
title: Time-uniform inference via supermartingales 
usekatex: true
published: false
---

## The problem of statistical inference

Statistical inference is all about *infering* the parameter of interest from the observed data.

Formally, let $\theta^\star \in \Theta$ be the true parameter of interest which gives rise to the data through some generative process $P(Y\mid \theta^\star)$ (if we allow for covariates, we denote the process by $P(Y\mid X, \theta^\star)$).
Upon observing the data $\mathcal{D} := \\{Y_1, Y_2, \ldots, Y_n\\}$, we often want to infer the parameter $\theta^\star$, which can be thought as the reverse problem of the forward generative process.
We assume that these $Y_1, Y_2, \ldots, Y_n$ aren't a deterministic function of $\theta^\star$ (otherwise, we can just deterministically invert the whatever function that generates the data), but are rather represented as [random variables](https://en.wikipedia.org/wiki/Random_variable).
If you're a frequentist, your estimator would be some mapping $f: \mathcal{D} \to \Theta$ that maps data $\mathcal{D}$ to your estimate $\hat{\theta}$; for example, in the problem of mean estimation, one usual choice of $f$ is the empirical mean $\hat{\mu} = \frac{1}{n}\sum_{t=1}^n Y_t$.
Alles gut?

Now, the art lies in choosing the right estimator $f$ that is "good" in some sense.
What does "good" mean to a statistician?
There are a few properties that we usually want our estimator to satisfy:

- [Consistency](https://en.wikipedia.org/wiki/Consistency_(statistics)):
If we observe a lot of data, our estimate should kind of converge to the true parameter $\theta^\star$.
Why not exact convergence?
Because the data we see is *random*, and we can only hope that the estimate, a function of the data, converges to the true parameter in probability.

- [Unbiasedness](https://en.wikipedia.org/wiki/Bias_of_an_estimator):
In expectation over the randomness of the data, our estimate should be equal to the true parameter $\theta^\star$.
In mathematical terms, this means $\mathbb{E}\_{\mathcal{D}}[\hat{\theta}] = \theta^\star$.
Under the [mean squared error](https://en.wikipedia.org/wiki/Bias%E2%80%93variance_tradeoff#Bias%E2%80%93variance_decomposition_of_mean_squared_error), the bias of an estimator is expressed as $\text{bias}(f) := (\mathbb{E}\_{\mathcal{D}}[\hat{\theta}] - \theta^\star)^2$.
In short, unbiaseness means $\text{bias}(f) = 0$.
It is worth to emphasize that consistency does not imply unbiasedness; you can think of estimators whose bias depends inversely on the sample size (e.g. $\text{bias}(f) = \mathcal{O}(1/\text{poly(n)})$), therefore the bias goes to zero as the sample size increases.
Biasedness is not necessarily a bad thing, in fact, there are biased estimators that outperform unbiased ones in terms of mean squared error (see [James-Stein estimator](https://en.wikipedia.org/wiki/James%E2%80%93Stein_estimator)).

- [Efficiency](https://en.wikipedia.org/wiki/Efficiency_(statistics)):
It is a measure of how much information we can extract from the data, often measured by the variance of the estimator.
Under the mean squared error setting like before, the variance of the estimator is defined as $\text{var}(f) := \mathbb{E}\_{\mathcal{D}}[(\hat{\theta} - \mathbb{E}\_{\mathcal{D}}[\hat{\theta}])^2]$.
There's in fact a [Cramér-Rao bound](https://en.wikipedia.org/wiki/Cram%C3%A9r%E2%80%93Rao_bound) that gives a lower bound on the variance of any unbiased estimator.
Any estimator that achieves this bound is called efficient.
Intuitively, an efficient estimator should ensure that, if we collect multiple datasets $\mathcal{D}_1, \mathcal{D}_2, \ldots$, the respective estimates $\hat{\theta}_1, \hat{\theta}_2, \ldots$ should be close to each other.

## Confidence sets and the quest for time-uniform guarantees

The business of defining a "good" estimator can go on and on depending on the statistician's goals, but the above properties are the most common ones.
This estimation process is often called *point estimation*.
We look at the data and all we say in the end is a single point $\hat{\theta}$ capturing everything we believe about the true parameter $\theta^\star$.
You should feel, at least, a bit uncomfortable about this.
Since the data is random, the estimate $\hat{\theta}$ is also random.
What if we don't have enough data to confidently make a claim about the true parameter?
Quantifying the uncertainty of the produced estimate is therefore a natural next step and a common practice in statistics.
This is where *confidence sets* come into play.

## Parameter space inference

We observe $n$ samples $\\{(Y_t, x_t)\\}_{t=1}^n$ that follows the linear regression model

$$
    Y_t = \langle x_t, \theta_\star \rangle + \eta_t,
$$

where the design vector $x_t \in \mathbb{R}^d$ is fixed and known, the noise $\eta_t$ is drawn i.i.d from a Gaussian $\mathcal{N}(0, 1)$ and the parameter of interest is $\theta_\star \in \mathbb{R}^d$.
For simplicity, we assume that the Gram matrix $V:= X^\top X = \sum_{t=1}^n x_t x_t^\top$ is non-singular (here, $X = [x_1^\top, \ldots, x_n^\top]^\top$).
For brevity, we can write $Y = X\theta_\star + \eta$ with $Y\in \mathbb{R}^n$ and $\eta \in \mathbb{R}^n$.

Our goal is to construct a sequence of confidence sets $C_1, C_2, \ldots$ such that $\Pr(\exists n \ge 1: \theta_\star \notin C_n) \le \delta$ for some confidence level $\delta \in (0, 1)$.
We will start with the maximum-likelihood estimator and then construct the confidence set for a certain $n$. 
Under the above assumptions, the maximum-likelihood estimator $\hat{\theta}$ is given by

$$
    \hat{\theta} = V^{-1} X^\top Y,
$$

which turns out to be a Gaussian because $Y$ is a Gaussian vector as given. 
In particular, we can write

$$
\begin{align*}
    \hat{\theta} &\sim \mathcal{N}(\theta_\star,  V^{-1}) \\
     V^{1/2}(\hat{\theta} - \theta_\star) &\sim \mathcal{N}(0, I_d)
\end{align*}
$$

Let $Z :=  V^{1/2}(\hat{\theta} - \theta_\star)$, then we have $\lVert Z \rVert_2^2 =  \lVert\hat{\theta}-\theta_\star\rVert_{V}^2$ follows a $\mathcal{X}\_d^2$-distribution with $d$ degrees of freedom.
From [the tail bounds of the $\chi^2$-distribution](https://stats.stackexchange.com/a/4821/301376), we have

$$
\begin{align*}
    \Pr\left(\|Z\|_2^2 \geq d + 2\sqrt{d\log(1/\delta)} + 2\log(1/\delta)\right) &\leq \delta
\end{align*}
$$

So if we define the confidence set $C_n$ as

$$
    C_n = \left\{\theta \in \mathbb{R}^d: \|\hat{\theta}-\theta\|_{V}^2 \leq  d + 2\sqrt{d\log(1/\delta)} + 2\log(1/\delta)\right\},
$$

then it is a $(1-\delta)$-confidence set for $\theta_\star$.

Using union bound, we can construct a sequence of confidence sets $C_1, C_2, \ldots$ such that $\Pr(\exists n \ge 1: \theta_\star \notin C_n) \leq \delta$ by choosing a larger confidence set for each $n$:

$$
    C_n = \left\{\theta \in \mathbb{R}^d: \|\hat{\theta}-\theta\|_{V}^2 \leq  d + 2\sqrt{d\log(n(n+1)/\delta)} + 2\log(n(n+1)/\delta)\right\}.
$$

Geometrically, the confidence set $C_n$ is an ellipsoid centered at $\hat{\theta}$ with the principal axes aligned with the eigenvectors of the Gram matrix $V$.
The length of the axes is determined by the eigenvalues of the Gram matrix $V$ and the confidence level $\delta$.
To see this, for any confidence set that is of the form $C_n = \{\theta :\lVert \hat{\theta} - \theta\rVert_V^2 \le \beta\}$, we can rewrite it as

$$
    C_n = \hat{\theta} + \beta^{1/2} V^{-1/2} \mathcal{B}_d,
$$

where $\mathcal{B}_d$ is the unit $\ell_2$-ball in $\mathbb{R}^d$.
Furthermore, the volume of this ellipsoid is exactly the determinant of $\beta^{1/2} V^{-1/2}$.
The calculation of this determinant is a bit dense but it invokes the *elliptical potential lemma* (see Lemma 19.4 in [Bandit Algorithms](https://tor-lattimore.com/downloads/book/book.pdf)) which turns out to scale as $\mathcal{O}\left(\frac{\log n}{n}\right)^{d/2}$.
This basically agrees with the classical concentration rate $\mathcal{O}(1/\sqrt{n})$ from the [Hoeffding's inequality](https://en.wikipedia.org/wiki/Hoeffding%27s_inequality) in the 1-dimensional case, where the extra $\log n$ factor arises due to the time-uniform guarantee.
