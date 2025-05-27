---
layout: posts
title: Model averaging with heterogeneous predictors
usekatex: true
published: true
---

Competitors on Kaggle always employ model averaging to improve their scores.
The resulting improvement, often achieved via a weighted average of predictors, is usually guaranteed and sometimes significant.
Here, I notice that a common piece of advice is to [average the predictions over a diverse pool of models](https://www.kaggle.com/competitions/playground-series-s5e4/discussion/575784), with a justification that models trained diffrerently are likely to make different errors and thus, averaging them will somehow reduce the overall error.
<!-- I find this explanation a bit vague and not very satisfying 🫠. -->
In this post, I will provide a more rigorous justification for model averaging with heterogeneous predictors.

Formally, let $f: \mathcal{X} \to \mathbb{R}$ be the ground truth function and $\hat{f}_1, \ldots, \hat{f}_k: \mathcal{X} \to \mathbb{R}$ be the predictors you have trained to approximate it.
<!-- Towards the end of a competition, these predictors are likely to be heterogeneous in the sense that they were trained with different hyperparameters, different architectures and different sets of features. -->
I can write these predictors as the ground truth function plus a residual error term:

$$
\hat{f}_i(x) = f(x) + \varepsilon_i(x), \quad i = 1, \ldots, k,
$$

In a typical Kaggle regression task, the mean squared error (MSE) of an individual predictor $\hat{f}_i$ is given by:

$$
    \text{MSE}(\hat{f}_i) := \mathbb{E}_{x \sim \mathcal{D}} [(\hat{f}_i(x) - f(x))^2] = \mathbb{E} [\varepsilon_i(x)^2] = \underbrace{\left(\mathbb{E}[\varepsilon_i]\right)^2}_{\text{bias}^2} + \text{Var}(\varepsilon_i)
$$

For concreteness, I assume the covariates $x$ are sampled from some distribution $\mathcal{D}$, and the expectation is taken with respect to this distribution.
For brevity, I will simply write $\varepsilon_i$ instead of $\varepsilon_i(x)$.
Now, the average of these predictors is given by:

$$
\bar{f} = \frac{1}{k} \sum_{i=1}^k \hat{f}_i = f + \frac{1}{k} \sum_{i=1}^k \varepsilon_i.
$$

And the corresponding MSE of the average predictor $\bar{f}$ is expressed as:

$$
\begin{align*}
    \text{MSE}(\bar{f})
    = \mathbb{E}_{x \sim \mathcal{D}}[(\bar{f} - f)^2]
    &= \left(\frac{1}{k} \sum_{i=1}^k \mathbb{E}[\varepsilon_i]\right)^2 + \frac{1}{k^2} \sum_{i=1}^k \text{Var}(\varepsilon_i) + \frac{2}{k^2} \sum_{i < j} \text{Cov}(\varepsilon_i, \varepsilon_j)
\end{align*}
$$

If we assume that these predictors are uncorrelated, i.e., $\text{Cov}(\varepsilon_i, \varepsilon_j) = 0$ for $i \neq j$, then the mean squared error of $\bar{f}$ should be no worse than the average of the individual predictors' mean squared errors:

$$
\begin{align*}
    \text{MSE}(\bar{f}) &= \left(\frac{1}{k} \sum_{i=1}^k \mathbb{E}[\varepsilon_i]\right)^2 + \frac{1}{k^2} \sum_{i=1}^k \text{Var}(\varepsilon_i) \\
    &\le \frac{1}{k} \sum_{i=1}^k (\mathbb{E}[\varepsilon_i])^2 + \frac{1}{k} \left(\frac{1}{k} \sum_{i=1}^k \text{Var}(\varepsilon_i)\right)
    \ll \frac{1}{k} \sum_{i=1}^k \text{MSE}(\hat{f}_i).
\end{align*}
$$

The first inequality above follows from Jensen's inequality.
Notice the additional $1/k$ factor in front of the averaged variance term.
If the predictors have approximately the same bias, i.e., $\mathbb{E}[\varepsilon_i]$ is similar across predictors, then model averaging effectively reduces the variance of the prediction error by a factor of $1/k$.
Otherwise, the averaged prediction $\bar{f}$​ inherits a bias equal to the average of the individual biases, but can still achieve significantly lower variance than any single predictor.
This illustrates a classic bias–variance trade-off.
Relaxing the assumption of uncorrelated predictors, the averaging can still yield improvements when $\text{Cov}(\varepsilon_i, \varepsilon_j) < 0$ for some $i \neq j$, since anti-correlated residual errors lead to cancellation with the positive correlation terms, or even greater overall reductions to the MSE since adding negative terms definitely helps.
Thus, having a diverse set of heterogeneous predictors is especially beneficial, as it allows model averaging to exploit these negative correlations.

In practice, people often use a weighted version of averaging:

$$
\bar{f} = \sum_{i=1}^k w_i \hat{f}_i, \quad \text{with } w_i \ge 0 \text{ and } \sum_{i=1}^k w_i = 1.
$$

We tune the weights $w_i$ (usually via [quadratic programming](https://en.wikipedia.org/wiki/Quadratic_programming) or [hill climbing](https://www.kaggle.com/competitions/siim-isic-melanoma-classification/discussion/175614)) to minimize the mean squared error of the average predictor $\bar{f}$ on an out-of-fold validation set.
The advantage of this approach over the simple average is that it allows you to turn the bias-variance trade-off into an optimization problem: if one of the predictors has a very high bias, it will probably get a very low weight, or if it has a good amount of anti-correlated residual errors, it might help reduce the overall MSE and therefore get a higher weight.
