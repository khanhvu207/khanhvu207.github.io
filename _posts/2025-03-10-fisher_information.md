---
layout: posts
title: What is Fisher information?
usemathjax: true
published: true
---

It bugs me that I often have hard time to recall what [Fisher information](https://en.wikipedia.org/wiki/Fisher_information) is.
So let's write it down here.

At the high-level view, Fisher information shows us how the observations constrain the possible values of the parameter of interest; or to put it another way, how informative the data is about the parameter.
There are several ways to see Fisher information, here are some of them:

- Fisher information is the variance of the [score](https://en.wikipedia.org/wiki/Informant_(statistics)).
- Fisher information measures the curvature of the [log-likelihood function](https://en.wikipedia.org/wiki/Likelihood_function).

I will try to explain the first point in this post, the second point might follow naturally from the math.
We will consider the setting where the parameter of interest is a vector $$\theta \in \Theta \subseteq \mathbb{R}^d$$ and $$X = \{x_t\}_{t=1}^n \in \mathcal{X}$$ are the observations that are drawn i.i.d from some unknown distribution $$p_{\theta}$$.

## Score function

We define the score function $$s: \Theta \to \mathbb{R}^d$$ as the gradient of the log-likelihood function, i.e.,

$$
    s(\theta; X) := \nabla_{\theta} \log p(X | \theta) = \sum_{t=1}^n \nabla_{\theta} \log p(x_t | \theta).
$$

Although score function seems like a function of the parameter, it is actually a $$\color{darkorange}{\text{random variable}}$$ that depends on the data $$X$$.
Also, from the above, the score function is the sum of the score functions of the individual observations.
Intuitively, the score function tells us the direction in which we should move the parameter to increase the likelihood of the data.
We typically solve for the maximum-likelihood estimate by setting the score function to zero, i.e., $$\theta_\text{MLE}$$ is the solution to $$s(\theta; X) = \mathbf{0}$$.

## Fisher information

To begin with, we can show that the expected score is zero, i.e.,

$$
\begin{align*}
    \mathbb{E}_{X\sim p_{\theta}}[s(\theta; X)] &= \mathbb{E}_{X\sim p_{\theta}}\left[\nabla_{\theta} \log p(X | \theta)\right] \\
    &= \mathbb{E}_{X\sim p_{\theta}}\left[\frac{1}{p(X | \theta)}\nabla_{\theta} p(X | \theta)\right] \\
    &= \int_{\mathcal{X}} p(x | \theta)  \frac{1}{p(x | \theta)}\nabla_{\theta} p(x | \theta) d x \\
    &= \nabla_{\theta} \int_{\mathcal{X}} p(x | \theta) d x \quad\text{(by the Leibniz rule)} \\
    &= \nabla_{\theta} 1 = \mathbf{0}.
\end{align*}
$$

Intuitively, this makes sense because the true parameter should be the one that maximizes the likelihood function, and the gradient of the log-likelihood function at the maximum vanishes.

Now, Fisher information is a mapping $$\mathcal{I}(\theta) : \Theta \to \mathbb{R}^{d \times d}$$ that measures the variance of the score function at some parameter $$\theta$$, i.e.,

$$
\begin{align*}
    \mathcal{I}(\theta) &:= \mathbb{V}_{X\sim p_{\theta}}[s(\theta; X)] \\
    &= \mathbb{E}_{X\sim p_{\theta}}\left[(s(\theta; X) - \mathbb{E}_{X\sim p_{\theta}}[s(\theta; X)])(s(\theta; X) - \mathbb{E}_{X\sim p_{\theta}}[s(\theta; X)])^\top\right] \\
    &= \mathbb{E}_{X\sim p_{\theta}}\left[s(\theta; X)s(\theta; X)^\top\right] - \underbrace{\mathbb{E}_{X\sim p_{\theta}}[s(\theta; X)]\mathbb{E}_{X\sim p_{\theta}}[s(\theta; X)]^\top}_{=0~\text{(as shown above)}} \\
    &= \mathbb{E}_{X\sim p_{\theta}}\left[s(\theta; X)s(\theta; X)^\top\right] \\
    &= \mathbb{E}_{X\sim p_{\theta}}\left[\nabla_{\theta} \log p(X | \theta) \nabla_{\theta} \log p(X | \theta)^\top\right]
\end{align*}
$$

Unlike score function, Fisher information matrix is a $$\color{darkgreen}{\text{deterministic quantity}}$$ that depends only on the parameter.

[Alternatively](https://en.wikipedia.org/wiki/Fisher_information#Matrix_form), one can write the Fisher information matrix as the negative expectation of the Hessian of the log-likelihood function, i.e.,

$$
    \mathcal{I}(\theta) = -\mathbb{E}_{X\sim p_{\theta}}\left[\nabla_{\theta}^2 \log p(X | \theta)\right]
$$

Under this formulation, it measure the curvature of the log-likelihood function at the parameter $$\theta$$.

It is worth pointing out that $$\theta$$ plays _two different roles_ in this context: it is the parameter of the distribution $$p_{\theta}$$ that gives rise to the data $$X$$ and the argument of the score function $$s(\theta; X)$$. 
I think this is the source of confusion for me.
Previously, I thought that the argument of the score function can be any parameter $$\tilde{\theta}$$ other than the parameter $$\theta$$ that generated the data, but this is not the case.
Why should these two be the same?
In a way, it tells us how accurate the maximum-likelihood estimate $$\theta_\text{MLE}$$ is;
if the log-likehood function is sharp (curvature is high), we are fairly confident about the estimate, and vice versa, if the log-likelihood function is flat (curvature is small), we are not so sure about the estimate.

<!-- In relation to [relative entropy](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence), it [turns out to be](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence#Fisher_information_metric)
the Hessian of the mapping $$\tilde{\theta} \mapsto \text{KL}(p_{\theta} || p_{\tilde{\theta}})$$ evaluated at $$\tilde{\theta} = \theta$$, i.e.,

$$
    \mathcal{I}(\theta) = \nabla_{\tilde{\theta}}^2 \text{KL}(p_{\theta} || p_{\tilde{\theta}}) \bigg|_{\tilde{\theta} = \theta}
$$ -->
