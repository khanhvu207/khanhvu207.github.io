---
layout: posts
title: Some personal notes on probability theory 
usemathjax: true
published: true
---

A self-contained summary of some key results in probability theory.

## 1. Law of large numbers

**(Convergence in probability)**
We say that $X_n$ converges to $X$ in probability if for every $\epsilon > 0$, $\lim_{n \to \infty} \Pr(|X_n - X| > \epsilon) = 0$, denoted by $X_n \overset{p}{\longrightarrow} X$.

**(Convergence in $L^r$)**
We say that $X_n$ converges to $X$ in $L^r$ if $\lim_{n \to \infty} \mathbb{E}(|X_n - X|^r) = 0$, denoted by $X_n \overset{L^r}{\longrightarrow} X$. 

**(Uncorrelated variables)**
Two random variables $X$ and $Y$ are uncorrelated if $\mathbb{E}(XY) = \mathbb{E}(X)\mathbb{E}(Y)$.

**(Lemma 1.1)**
Let $X_1, X_2, \ldots$ be a sequence of uncorrelated random variables with $\mathbb{E}(X_i) < \infty$, then we have that $\mathbb{V}(X_1 + \ldots + X_n) = \mathbb{V}(X_1) + \ldots + \mathbb{V}(X_n)$.

_Proof._ Omitted for brevity.

---
<p style="background-color: lightblue; padding: 10px;">
$\color{darkblue}{(\text{Theorem 1.1: $L^2$ weak law})}$
Let $X_1, X_2, \ldots$ be a sequence of uncorrelated random variables with $\mathbb{E}(X_i) = \mu$ and $\mathbb{V}(X_i) \le C < \infty$.
If $S_n := X_1 + \ldots + X_n$, then $S_n/n \overset{L^2}{\longrightarrow} \mu$.
</p>

_Proof._
We have that $\mathbb{E}[|S_n/n - \mu|^2] = \mathbb{V}(S_n/n) \le \frac{C/n}{n^2} \to 0$ as $n \to \infty$.
Here, we used Lemma 1.1 to compute the variance of $S_n/n$.

---
**(Chebyshev's inequality)**
For any random variable $X$ and $r > 0$, $\Pr(|X| \ge \epsilon) \le \epsilon^{-r}\mathbb{E}(|X|^r)$.

One small remark is that Chebyshev's inequality is generally not a sharp inequality (for example try bounding standard normal variables), however, it cannot be improved in [certain cases](https://en.wikipedia.org/wiki/Chebyshev%27s_inequality#Sharpness_of_bounds).

**(Lemma 1.2: Convergence in $L^r$ implies convergence in probability)**
For $r > 0$, if $Z_n \overset{L^r}{\longrightarrow} 0$, then $Z_n \overset{p}{\longrightarrow} 0$.

_Proof._ Since $\mathbb{E}(\|Z_n\|^r) \to 0$, the proof follows directly from Chebyshev's inequality applying to the random variable $Z_n$.

<p style="background-color: lightblue; padding: 10px;">
$\color{darkblue}{(\text{Theorem 1.2: Weak law of large numbers})}$
$S_n/n \overset{p}{\longrightarrow} \mu$.
</p>

_Proof._ Follows from Lemma 1.2 with $r = 2$ and the $L^2$ weak law.

Remarkably, the weak law of large numbers does not require the random variables to be independent and identically distributed (i.i.d), only that they are uncorrelated.
