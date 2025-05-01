---
layout: posts
title: Approximating self-attention with random features
usemathjax: true
published: false
---
$$
\newcommand{\bm}{\mathbf}
\newcommand{\expect}{\mathbb{E}}
\newcommand{\norm}[1]{\|#1\|}
\newcommand{\ip}[2]{\langle#1,#2\rangle}
\newcommand{\since}[1]{\left(#1\right)}
\newcommand{\oh}[1]{\mathcal{O}\left(#1\right)}
$$

<!-- As of today, we are witnessing the upsurge in adoptions of a new class of algorithms coined by the infamous term *artificial intelligence*. 
No wonder, ChatGPT is one of the kind that would likely to arise in any AI-related conversation. -->
Today, we are witnessing a remarkable surge in the adoption of a revolutionary class of algorithms known as artificial intelligence.
It comes as no surprise that ChatGPT stands out as a leading example in any AI-related conversation.
In essence, ChatGPT is a digital marvel that ingests a sequence of bytes and ingeniously generates a statistically probable response sequence.
On one hand, ChatGPT demonstrates the ability to solve tasks that traditionally require Turing-complete systems, such as reversing a list of numbers, duplicating the list, and so on.
On the other hand, it is nothing more than a large Transformer neural net which, in turn, presents certain limitations due to a predefined context window.

Currently, the most recent version of ChatGPT has a context window of 16,000 tokens, which is typically sufficient for everyday conversational purposes.
However, it is essential to recognize that this 16K context window pales in comparison to the vast cognitive capabilities of humans.
Humans can recall significant events from the distant past as long as they hold relevance in memory.
In this blog post, I will explore a fascinating approach to surpassing this limitation.
The fundamental concept was initially introduced by [(Rahimi and Recht, 2007)](https://people.eecs.berkeley.edu/~brecht/papers/07.rah.rec.nips.pdf) and has since been refined for self-attention in [(Choromanski et al., 2020)](https://arxiv.org/abs/2009.14794).

### An inherent limitation of self-attention

<center>
    <figure>
        <img src="https://peterbloem.nl/files/transformers/self-attention.svg" alt="Vanilla self-attention" style="width:600px; display: inline-block;"/>
        <figcaption>Self-attention for a squence of length 4. Illustration by <a href="https://peterbloem.nl/blog/transformers">Peter Bloem</a>.</figcaption>
    </figure>
</center> 

The fundamental building block of Transformers is the *self-attention* mechanism.
The idea has been proposed from the 90s and is recently popularized by [(Vaswani et al., 2016)](https://arxiv.org/abs/1706.03762).
<!-- Initially, the input sequence is transformed differently into embedding space $\mathbb{R}^D$. -->
<!-- Here, the transformed input is collected into matrices $\bm{Q}, \bm{K}, \bm{V}$ $\in \mathbb{R}^{\ell \times D}$. -->
In a typical self-attention layer, each element in the output sequence is a convex sum of the elements constituting the input sequence.
The weights parameterizing the convex sums are computed via a positive-definite kernel $\kappa:\mathbb{R}^D \times \mathbb{R}^D \rightarrow \mathbb{R}_+$, giving us a notion of similarity/compatability measure between the pairs of input elements.
For the most time, the *softmax kernel* $$\kappa_\text{SM}(\bm{x}, \bm{y})=\exp(\bm{x}^\top\bm{y})$$ is being used.
Optionally, we apply linear transformation to the sequence of input embeddings and get three additional sequences of embeddings, namely $$\bm{Q}, \bm{K}, \bm{V} \in \mathbb{R}^{\ell \times D}$$.
The self-attention layer is then transcribed as follows:

$$
\begin{align*}
\mathtt{Attention}(\bm{Q},\bm{K},\bm{V}) &= \bm{D}^{-1}\bm{A}\bm{V}\\
\bm{A} &= \exp\left(\bm{Q}\bm{K}^\top\right)\\
\bm{D} &= \text{diag}(\bm{A} 1_\ell)\\
\end{align*}
$$

Since self-attention allows complete interactions between entities in the input sequence, the algorithm is computationally constrained by the quadratic dependency on the sequence's length $\ell$, i.e. self-attention is of $\oh{\ell^2D}$ time complexity. In the context of ChatGPT, we have $$\ell=16000$$, which is a big deal for a business with one hundred million users per month as the electric bill would also grow quadratically in $$\ell$$.

### A fast approximation to self-attention with random features

We can also explicitly write the attention matrix $\bm{A} \in \mathbb{R}^{\ell \times \ell}$ whose entries are pairwise evaluations of softmax kernel over the input sequence:

$$
\begin{align*}
\bm{A}=
\begin{pmatrix}
\kappa_\text{SM}(\bm{Q}_{1,:},\bm{K}_{1,:}) & \dots & \kappa_\text{SM}(\bm{Q}_{1,:},\bm{K}_{\ell,:}) \\
\vdots & \ddots & \vdots \\
\kappa_\text{SM}(\bm{Q}_{\ell,:},\bm{K}_{1,:}) & \dots & \kappa_\text{SM}(\bm{Q}_{\ell,:},\bm{K}_{\ell,:})
\end{pmatrix}
\end{align*}
$$

**Definition (Positive-definite kernel).** Let $\mathbb{V}$ be a Hilbert space and $\ip{\cdot}{\cdot}_\mathbb{V}$ be its corresponding inner product.
A positive-definite kernel $\kappa(\bm{x},\bm{y}):\mathbb{R}^D\times \mathbb{R}^D \rightarrow \mathbb{V}$ is a function for which there exists a feature map $\phi:\mathbb{R}^D \rightarrow \mathbb{V}$ so that:

$$
\kappa(\bm{x},\bm{y}) = \ip{\phi(\bm{x})}{\phi(\bm{y})}_\mathbb{V}
$$

By this definition, the softmax kernel can be written as $$\kappa_\text{SM}(\bm{x},\bm{y})=\ip{\phi_\text{SM}(\bm{x})}{\phi_\text{SM}(\bm{y})}$$ where the feature map is defined as:

$$
\begin{align*}
    \phi_{\text{SM}}(\bm{x}) &= \begin{bmatrix} 1 & \bm{x} & \frac{1}{\sqrt{2!}}\bm{x}^2 & \frac{1}{\sqrt{3!}}\bm{x}^3 & \cdots \end{bmatrix}^\top\\
    \phi_{\text{SM}}(\bm{y}) &= \begin{bmatrix} 1 & \bm{y} & \frac{1}{\sqrt{2!}}\bm{y}^2 & \frac{1}{\sqrt{3!}}\bm{y}^3 & \cdots \end{bmatrix}^\top
\end{align*}
$$

<center>
    <figure>
        <img src="https://1.bp.blogspot.com/--ulWTdkc74g/X5Ibm2gSpXI/AAAAAAAAGtA/bUJq-7HyGicl_PmsPICF9Fqi0lVGSHcEgCLcBGAsYHQ/s889/image8.jpg" alt="Ordinary least squares by OpenAI's DALLE" style="width:500px; display: inline-block;"/>
        <figcaption>$\textbf{Left}$: The standard attention matrix $\bm{A}\in\mathbb{R}^{\ell\times\ell}$. $\textbf{Right}$: Using definition for positive-definite kernels, we can decompose attention matrix $\bm{A}$ into two matrices $\bm{Q}'$ and $\bm{K}'$ which can be exploited to speed up the attention matrix computation later. Illustration from <a href="https://peterbloem.nl/blog/transformers">Rethinking Attention with Performers</a>.</figcaption>
    </figure>
</center> 

This is due to the Taylor expansion of the exponential function $e^x$. In particular:

$$
\begin{align*}
    \kappa_\text{SM}(\bm{x},\bm{y}) &= \exp(\bm{x}^\top \bm{y})\\
    &= \sum_{n=0}^\infty \frac{(\bm{x}^\top \bm{y})^n}{n!}\\
    &= \ip{\phi_\text{SM}(\bm{x})}{\phi_\text{SM}(\bm{y})}
\end{align*}
$$

It is worth noticing that the feature maps $\phi_\text{SM}(\bm{x})$ and $\phi_\text{SM}(\bm{y})$ are in fact of infinite dimension!
The softmax kernel implicitly measures the similarity between the $\bm{x}$ and $\bm{y}$ as if they has been projected into a much higher dimension.

This is a well-exploited idea in machine learning and is often called the *kernel trick*.
The method is useful in classification problems because it turns complicated inseparable feature space into separable one by lifting the features into higher dimensional space.
However, the motivation for kernelizing self-attention is very different from the separability problem as you will see below.

We introduce a theorem that allows us to approximate *any stationary kernel* through a randomized feature map $\xi(\cdot)$.

**Theorem 1 (Bochner's theorem).** Let $\xi(\cdot):\mathbb{R}^D \rightarrow \mathbb{R}^r$ be a random feature defined as follows.

$$
\xi(\bm{x}) = \frac{h(\bm{x})}{\sqrt{m}}
\begin{bmatrix}
f_1(\bm{\omega_1}^\top\bm{x}),\dots,f_1(\bm{\omega_m}^\top\bm{x}),f_l(\bm{\omega_1}^\top\bm{x}),\dots,f_l(\bm{\omega_m}^\top\bm{x})
\end{bmatrix}
$$

where $r=m\cdot l$, $f_1,\dots,f_l:\mathbb{R}\rightarrow\mathbb{R}$, $h:\mathbb{R}^D\rightarrow\mathbb{R}$ and $\bm{w_i}\in\mathbb{R}^D$ are drawn i.i.d from $\mathcal{D}$ for a probability distribution $\mathcal{D}$. A stationary kernel $\kappa(\bm{x},\bm{y})$ can be approximated as:

$$
\kappa(\bm{x},\bm{y}) = \ip{\phi(\bm{x})}{\phi(\bm{y})}_\mathbb{V} = \expect_{\bm{w}\sim\mathcal{D}}\left[\ip{\xi(\bm{x})}{\xi(\bm{y})}_\mathbb{V}\right]
$$

The softmax kernel $\kappa_\text{SM}(\bm{x},\bm{y})$ can be approximated using the above theorem as:

$$
\kappa_\text{SM}(\bm{x},\bm{y})=\expect_{\bm{w}\sim\mathcal{N}(\bm{0}_D,\bm{I}_D)}\left[\ip{\xi_\text{SM}(\bm{x})}{\xi_\text{SM}(\bm{y})}\right]
$$

where $\xi_\text{SM}(\bm{x})=\frac{\exp\left(-\frac{\norm{\bm{x}}^2}{2}\right)}{\sqrt{m}}\begin{bmatrix}\exp(\bm{\omega_1}^\top\bm{x},\dots,\exp(\bm{\omega_m}^\top\bm{x}))\end{bmatrix}$

Using $\xi_\text{SM}(\bm{x})$, we introduce a form of kernelized attention defined as a Monte Carlo estimation:

$$
\mathtt{KernelizedAttention}(\bm{Q},\bm{K},\bm{V}) = \expect_{\bm{\omega}} \left[(\bm{D}')^{-1} \left(\bm{Q}' \left((\bm{K}')^\top \bm{V}\right) \right)\right]
$$

where $\bm{Q}', \bm{K}'$ and $\bm{D}'$ have the following form:

$$
\begin{align*}
\bm{Q}'[i,:] &= \xi_\text{SM}(\bm{Q}[i, :]^\top)^\top\\
\bm{K}'[i,:] &= \xi_\text{SM}(\bm{K}[i, :]^\top)^\top\\
\bm{D}' &= \text{diag}(\bm{Q}'((\bm{K}')^\top \bm{1}_\ell))
\end{align*}
$$

Therefore, the time complexity of this $\mathtt{KernelizedAttention}$ is of $\oh{\ell r D}$, which escapes from the quadratic dependency on the sequence length $\ell$.

<center>
    <figure>
        <img src="https://1.bp.blogspot.com/-pQ8s4X2qXjI/X5Ib6nLtxWI/AAAAAAAAGtI/C7dmMqV3Gu0NGYtmi5Gqjkr_Pqun5T2MwCLcBGAsYHQ/s1428/image10.jpg" alt="Ordinary least squares by OpenAI's DALLE" style="width:600px; display: inline-block;"/>
        <figcaption>$\textbf{Left}$: The canonical way to compute self-attention. $\textbf{Right}$: The low-rank decomposition of $\bm{A}$ into $\bm{Q'}(\bm{K'}^\top)$ allows us to leverage the associative property of linear maps. We first multiply $(\bm{K'}^\top)\bm{V}$ and then multiply $\bm{Q'}((\bm{K'}^\top)\bm{V})$ subsequently. Illustration from <a href="https://peterbloem.nl/blog/transformers">Rethinking Attention with Performers</a>.</figcaption>
    </figure>
</center> 

Additionally, it also reduce the required memory as the vanilla $\mathtt{Attention}$ would need $\oh{\ell^2 + \ell D}$ to store the attention matrix explicitly. Oppositely, $\mathtt{KernelizedAttention}$ only require $\oh{\ell r + rD + \ell D}$ of storage.

### (Bonus) Important properties of the softmax kernel estimator

Let $$\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})} = \ip{\xi_\text{SM}(\bm{x})}{\xi_\text{SM}(\bm{y})}$$.

**Proposition 1.** The estimator $$\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}$$ is an unbiased estimator of $$\kappa_\text{SM}(\bm{x},\bm{y})$$.

<u>Proof:</u>

$$
\begin{align*}
   \expect \left[\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}\right] &= \frac{1}{m}\exp\left(-\frac{(\norm{\bm{x}}^2 + \norm{\bm{y}}^2)}{2}\right)\left(\sum_{i=1}^m \expect \left[\exp(\bm{\omega}_i^\top(\bm{x}+\bm{y}))\right]\right)\\
   &=\exp\left(-\frac{(\norm{\bm{x}}^2 + \norm{\bm{y}}^2)}{2}\right) \exp\left(\frac{\norm{\bm{x}+\bm{y}}^2}{2}\right)\\
   &=\exp\left(\bm{x}^\top\bm{y}\right) = \kappa_\text{SM}(\bm{x},\bm{y})
\end{align*}
$$

The second equality use the following fact:

$$
\begin{align*}
    \expect \left[\exp(\bm{\omega}^\top(\bm{x}+\bm{y}))\right] &=\int \exp(\bm{\omega}^\top(\bm{x}+\bm{y}))(2\pi)^{-D/2}\exp\left(-\frac{\norm{\bm{\omega}}^2}{2}\right)d\bm{\omega}\\
    &=(2\pi)^{-D/2} \int \exp\left(-\frac{(\norm{\bm{\omega}}^2 -2\bm{\omega}^\top(\bm{x}+\bm{y}) + \norm{\bm{x}+\bm{y}}^2 - \norm{\bm{x}+\bm{y}}^2)}{2}\right) d\bm{\omega}\\
    &=\exp\left(\frac{\norm{\bm{x}+\bm{y}}^2}{2}\right)(2\pi)^{-D/2} \int \exp\left(-\frac{\norm{\bm{\omega} - (\bm{x}+\bm{y})}^2}{2}\right) d\bm{\omega}\\ 
    &=\exp\left(\frac{\norm{\bm{x}+\bm{y}}^2}{2}\right)
\end{align*}
$$

**Proposition 2.** The estimator $$\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}$$ is consistent when $$\kappa_\text{SM}(\bm{x},\bm{y})\rightarrow 0$$. 

<u>Proof:</u> 

Before we analyze the risk $\text{MSE}\left(\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}\right)$, we will need the derivation to the $2^{nd}$ moment of $\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}$:

$$
\begin{align*}
    &\expect \left[\left(\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}\right)^2\right]\\
    &=\frac{1}{m^2}\exp\left(-\frac{(\norm{\bm{x}}^2 + \norm{\bm{y}}^2)}{2}\right)^2 \expect\left(\sum_{i=1}^m \exp(\bm{\omega}_i^\top(\bm{x}+\bm{y}))^2\right)\\
    &=\frac{1}{m^2}\exp\left(-\frac{(\norm{\bm{x}}^2 + \norm{\bm{y}}^2)}{2}\right)^2 \expect \left[\sum_{i=1}^m \exp\left(\bm{\omega}_i^\top(\bm{x}+\bm{y})\right)^2+2\sum_{i=1}^{m-1}\sum_{j=i+1}^m \exp(\bm{\omega}_i^\top(\bm{x}+\bm{y}))\exp(\bm{\omega}_j^\top(\bm{x}+\bm{y}))\right]\\
    &=\frac{1}{m^2}\exp\left(-\norm{\bm{x}}^2 -\norm{\bm{y}}^2\right)\left(m\cdot\exp\left(2\norm{\bm{x}+\bm{y}}^2\right) + m(m-1)\exp\left(\norm{\bm{x}+\bm{y}}^2\right)\right)\\
    &=\frac{1}{m}\exp\left(-\norm{\bm{x}}^2 -\norm{\bm{y}}^2 + 2\norm{\bm{x}+\bm{y}}^2\right) + \frac{m-1}{m} \exp\left(-\norm{\bm{x}}^2 -\norm{\bm{y}}^2 + \norm{\bm{x}+\bm{y}}^2\right)\\
    &=\frac{1}{m}\exp\left(\norm{\bm{x}+\bm{y}}^2\right)\exp(2\bm{x}^\top\bm{y}) + \frac{m-1}{m} \exp\left(2\bm{x}^\top\bm{y}\right)\\
    &=\frac{1}{m}\exp\left(\norm{\bm{x}+\bm{y}}^2\right)\kappa_\text{SM}(\bm{x},\bm{y})^2 + \frac{m-1}{m}\kappa_\text{SM}(\bm{x},\bm{y})^2\\
\end{align*}
$$

Putting everything together, the risk function is derived as follows:

$$
\begin{align*}
    &\text{MSE}\left(\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}\right)\\
    &=\expect\left[\left(\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}_+ - \kappa_\text{SM}(\bm{x},\bm{y})\right)^2\right]\\
    &=\expect\left[\left(\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}_+\right)^2\right] - 2\kappa_\text{SM}(\bm{x},\bm{y})\expect\left[\widehat{\kappa_\text{SM}(\bm{x},\bm{y})}^{(\bm{\omega})}_+\right] + \kappa_\text{SM}(\bm{x},\bm{y})^2\\
    &=\frac{1}{m}\exp\left(\norm{\bm{x}+\bm{y}}^2\right)\kappa_\text{SM}(\bm{x},\bm{y})^2 + \frac{m-1}{m}\kappa_\text{SM}(\bm{x},\bm{y})^2 - 2\kappa_\text{SM}(\bm{x},\bm{y})\kappa_\text{SM}(\bm{x},\bm{y}) + \kappa_\text{SM}(\bm{x},\bm{y})^2 \quad\text{(Using proposition 1)}\\
    &=\frac{1}{m}\exp\left(\norm{\bm{x}+\bm{y}}^2\right)\kappa_\text{SM}(\bm{x},\bm{y})^2 - \frac{1}{m}\kappa_\text{SM}(\bm{x},\bm{y})^2 \\
    &=\frac{1}{m}\kappa_\text{SM}(\bm{x},\bm{y})^2\left(\exp\left(\norm{\bm{x}+\bm{y}}^2\right) - 1\right)\\
    &=\frac{1}{m}\kappa_\text{SM}(\bm{x},\bm{y})^2\left(\exp\left(\norm{\bm{x}+\bm{y}}^2\right) - \exp\left(-\norm{\bm{x}+\bm{y}}^2\right)\exp\left(\norm{\bm{x}+\bm{y}}^2\right)\right)\\
    &=\frac{1}{m}\exp\left(\norm{\bm{x}+\bm{y}}^2\right)\kappa_\text{SM}(\bm{x},\bm{y})^2 \left(1 - \exp\left(-\norm{\bm{x}+\bm{y}}^2\right)\right)
\end{align*}
$$

Hence, the risk vanishes as $$\kappa_\text{SM}(\bm{x},\bm{y})\rightarrow 0$$.

<!-- The unpredictability of the universe is what enables the creation of nearly unbreakable algorithms.
From the most fundamental procedure such as sorting a list of numbers to reliably transmitting digital images across continents, there must be elements of randomness in its logic.
Randomness, to me, gives rise to modern computing. -->
