---
layout: posts
title: On the minimax optimality of ordinary least squares 
usemathjax: true
published: false
---
$$
\newcommand{\bls}{\mathbf{\hat{\beta}}_{\text{LS}}}
\newcommand{\expect}[1]{\mathbb{E}\left[#1\right]}
\newcommand{\err}[1]{\text{err}\left(#1\right)}
\newcommand{\norm}[1]{\|#1\|}
\newcommand{\ip}[2]{\langle #1, #2 \rangle}
\newcommand{\since}[1]{\left(#1\right)}
$$

<center>
    <figure>
        <img src="https://github.com/khanhvu207/khanhvu207.github.io/blob/main/img/dalle_ols.png?raw=true" alt="Ordinary least squares by OpenAI's DALLE" style="width:400px; display: inline-block;"/>
        <figcaption>Ordinary least squares. Illustrations by OpenAI's DALLE </figcaption>
    </figure>
</center> 

## The setup 

Let $X \in \mathbb{R}^{n \times d}$ be the design matrix such that $\frac{1}{n}X^\top X = \text{Id}_d$. 
Let $\beta^* \in \mathbb{R}^d, \textbf{w} \sim \mathcal{N}(0, \text{Id}_n)$, and

$$
\begin{align}
    \textbf{y} = X\beta^* + \textbf{w}
\end{align}
$$

In this problem, we are given observation $\textbf{y} \in \mathbb{R}^n$ and design matrix $X$. The goal is then to design an estimator $\hat{\beta}(\textbf{y})$ for $\beta^*$.
In essence, any estimator that seeks to recover $$\beta^*$$ can be viewed as a denoiser as we consequently remove the noise $\mathbf{w}$ from the data.

To students who study statistics, the canonical approach is to use the ordinary least squares (LS) estimator which is the minimizer to objective function $f(\beta)=\norm{\mathbf{y} - X\beta}^2$. The estimator $\bls = \text{argmin}~f(\beta)$ has a nicely closed-form solution.
In particular,

$$
\begin{align}
\bls &= (X^\top X)^{-1} X^\top \textbf{y} = \frac{1}{n} X^\top \mathbf{y}
\end{align}
$$

It is also clear that $\bls$ is an unbiased estimator, e.g. $\expect{\bls}=\beta^*$.

## Minimax optimality of ordinary least squares

We shall measure the quality of an estimator using the expected squared error:

$$
\err{\hat{\beta}} := \expect{\norm{\hat{\beta}(\mathbf{y})-\beta^*}^2}
$$

With a bit of algebraic manipulations, we have that

$$
\begin{align}
    \err{\bls} &= \expect{\norm{\bls(\mathbf{y}) - \beta^*}^2}\\
    &= \expect{\norm{\frac{1}{n}X^\top \mathbf{y} - \beta^*}^2}\\
    &= \expect{\norm{\frac{1}{n}X^\top (X\beta^* + \mathbf{w}) - \beta^*}^2}\\
    &= \expect{\norm{\frac{1}{n}X^\top \mathbf{w}}^2}\\
    &= \frac{1}{n^2} \expect{\text{Tr} XX^\top \mathbf{ww}^\top}\\
    &= \frac{1}{n^2} \text{Tr} XX^\top ~~\since{\text{Since }\expect{\mathbf{ww}^\top}=\text{Id}_n}\\
    &= \frac{1}{n^2} \text{Tr} X^\top X ~~\since{\text{By the cyclicity of trace}}\\
    &= \frac{d}{n}
\end{align}
$$

This statistical guarantee implies that $\bls$ is a consistent estimator, meaning if $n \rightarrow \infty$, then $\err{\bls}$ vanishes.
Furthermore, $\bls$ is minimax optimal in a sense that it achieves the minimum expected squared error in the worst case considering all $\beta^* \in \mathbb{R}^d$.
Concretely, we have the following theorem:

$$
\inf_{\hat\beta\colon\mathbb R^n\to \mathbb R^d}~ \sup_{\beta^\star\in\mathbb R^d}~ \mathop{\mathbb{E}}_{\boldsymbol{y} = X\beta ^\star+ \boldsymbol{w}} ~ \tfrac 1n \left\lVert X\hat\beta(\boldsymbol{y})-X \beta^\star\right\rVert^2=\frac dn
$$

The infimum is achieved by setting $\hat{\beta}$ to $\bls$.

**<u>Proof.</u>**


(*Interlude: Bayes-optimal estimator*) If $$y=X\beta +\mathbf{w}$$ with a Gaussian prior $$\beta \sim \mathcal{N}(0, t\cdot \text{Id}_d)$$, then the posterior is also a Gaussian:

$$
\{ X \boldsymbol{\beta }\mid \boldsymbol{y} \} = N(B_t \boldsymbol{y}, B_t)\,,
$$

where $B_t=X (X^\top X + \tfrac 1 t I_d)^{-1} X^\top$.
The Bayes-optimal estimator would be choosing the posterior mean $$X \hat\beta_{\mathrm{mean}}(\boldsymbol{y}) = \mathop{\mathbb{E}}[ X\boldsymbol{\beta }\mid \boldsymbol{y}] = B_t \boldsymbol{y}$$.
We determine the guarantees of Bayes-optimal estimators for the prior $$\mathcal{N}(0, t \cdot \text{Id}_d)$$:

$$
\mathop{\mathbb{E}}\tfrac 1 n \| X \hat\beta_{\mathrm{mean}}(\boldsymbol{y}) - X \boldsymbol{\beta }\|^2 = \tfrac 1 n \sum_{i=1}^d \tfrac {\lambda_i}{\lambda_i + 1/t}
$$

where $$\lambda_1, \ldots, \lambda_d \ge 0$$ are the eigenvalues of $$X^\top X$$.

(*Connection to OLS*) In the limit $t \rightarrow \infty$, we recover the ordinary least squares estimator:

$$
\lim_{t\to \infty} B_t \boldsymbol{y} = X (X^\top  X)^{-1} X^\top  \boldsymbol{y} = X \hat\beta_{\mathrm{LS}}(\boldsymbol{y})
$$

Coming back to the main proof, consider an arbitrary $$\beta^* \in \mathbb{R}^d$$ 

$$
\begin{aligned}
& \sup_{\beta^\star\in\mathbb R^d}\mathop{\mathbb{E}}_{\boldsymbol{y} = X\beta ^\star+ \boldsymbol{w}} \left\lVert X\hat\beta(\boldsymbol{y})-X\beta^\star\right\rVert^2\\
&\ge \mathop{\mathbb{E}}_{\boldsymbol{\beta}\sim N(0,t\cdot I_d)} \mathop{\mathbb{E}}_{\boldsymbol{y} = X\boldsymbol{\beta }+ \boldsymbol{w}} \left\lVert X\hat\beta(\boldsymbol{y})-X\boldsymbol{\beta}\right\rVert^2\\
&\ge \sum_{i=1}^d \frac{\lambda_i}{\lambda_i+1/t}
\end{aligned}
$$

The inequality continues to hold if we replace the right-hand side by its limit when $t \rightarrow \infty$. Then, we obtain

$$
\begin{aligned}
&\sup_{\beta^\star\in\mathbb R^d}\mathop{\mathbb{E}}_{\boldsymbol{y} = X\beta ^\star+ \boldsymbol{w}} \left\lVert X\hat\beta(\boldsymbol{y})-X\beta^\star\right\rVert^2
\ge \lim_{t\to \infty} \sum_{i=1}^d \frac{\lambda_i}{\lambda_i+1/t}=d 
\end{aligned}
$$

> Inspired by the first two lectures from the "Algorithmic Foundations of Data Science" course at ETH Zurich

<!-- ## Admissibility of James-Stein's estimator

<center>
    <figure>
        <img src="https://www.naftaliharris.com/blog/steinviz/steinhalf.gif" alt="Ordinary least squares by OpenAI's DALLE" style="width:200px; display: inline-block;"/>
        <img src="https://www.naftaliharris.com/blog/steinviz/steinone.gif" alt="Ordinary least squares by OpenAI's DALLE" style="width:200px; display: inline-block;"/>
        <img src="https://www.naftaliharris.com/blog/steinviz/steintwo.gif" alt="Ordinary least squares by OpenAI's DALLE" style="width:200px; display: inline-block;"/>
        <figcaption>James-Stein estimator applied to the points on the sphere. Illustrations by <a href="https://www.naftaliharris.com/blog/steinviz/">Naftali</a></figcaption>
    </figure>
</center> -->