---
tags:
  - Maths/Probability-Theory
  - Maths/Information-Theory
  - Note
finished-writing: true
aliases:
  - Cross Entropy
---
# Entropy

## Entropy

### Intuition

We can measure the "surprise" of a event $A$ using **negative logarithm** $-\log P(A)$.
- If $A$ happens with a high probability, i.e., $P(A) \to 1$, it is not surprising when $A$ happens. $-\log P(A)$ is closed to $0$.
- If $A$ happens with a low probability, i.e., $P(A) \to 0$, it is surprising when $A$ happens. $-\log P(A)$ is closed to $+\infty$.

### Definition
Given a distribution $p$, we define the entropy as the **expectation of surprise**:
$$
H(p) = -\mathbb{E}_{p}[\log p] = \begin{cases}
-\sum_{x \in \mathcal{X}} p(x) \log (x), & \text{for discrete $p$;}\\
-\int_{\mathcal{X}} p(x) \log p(x) \, \mathrm{d}x, & \text{for continuous $p$}.
\end{cases}
$$

### Interpretation

The entropy can be interpreted as a measure of **uncertainty**:
- If a distribution has high entropy, the observations are hard to predict, and the dataset sampled from the distribution will have high information content.
- If a distribution has small entropy, then observations are likely to be the same, and the dataset sampled from the distribution does not contain much information.

### Properties
For **discrete** $p$:
- $H(p)$ is always **non-negative**;
- $H(p)$ is at its maximum if $p$ is the **uniform distribution**;
- $H(p)$ is at its minimum if the probability mass is all in one state.

For **continuous** $p$:
- $H(p)$ can be negative;
- For **fixed mean and variance**, $H(p)$ is at its maximum if $p$ is [[Normal Distribution|Gaussian]].

## Examples

### Bernoulli Distribution

For a [[Bernoulli, Binomial and Categorical Distribution|Bernoulli distribution]] parameterized by $\theta$, if $X \sim \text{Bern}(\theta)$ such that $P(X = 1) = \theta$ and $P(X = 0) = 1 - \theta$:
$$
H(p) = -\theta \log \theta - (1- \theta) \log (1 - \theta).
$$
$H(p)$ is maximized when $\theta = \frac{1}{2}$.

### Gaussian Distribution

The entropy of a $d$-dimensional [[Normal Distribution|Gaussian]] is:

$$
H(\mathcal{N}(\boldsymbol{\mu}, \boldsymbol{\Sigma})) = \frac{1}{2} \log \det(2 \pi e \boldsymbol{\Sigma}) = \frac{d}{2} + \frac{d}{2}\log (2\pi) + \frac{1}{2}\log \det(\boldsymbol{\Sigma}).
$$
In univariate case,
$$
H(\mathcal{N}(\mu, \sigma^2)) = \frac{1}{2} \log (2 \pi e \sigma^2).
$$

## Entropy in Information Theory

Entropy is useful to **calculate the smallest amount of information** required to convey a message.

Consider the transmission of sequences with a set of characters $\mathcal{X}$. Given the frequency of each character $X \in \mathcal{X}$: $p(X)$, the **minimum expected number of bits** to encode a character is
$$
-\sum_{X \in \mathcal{X}} p(X)\log_{2}p(X),
$$
where each character is encoded with $-\log_{2} p(X)$ bits.

```ad-example
Suppose we want to encode a sequence of 4 characters `A, B, C, D` with equal frequency $0.25$. If we encode each character with $-\log_{2}(0.25) = 2$ bits, the expected number of bits to encode a character is at the minimum:
$$
-\sum_{i=1}^4 0.25 \times 2 = 2.
$$
In this case, `A, B, C, D` is encoded with `00, 01, 10, 11`.
```

## Cross Entropy

For two distributions $p$ and $q$, the **cross entropy** of $p$ relative to $q$ is
$$
H(p, q) = -\mathbb{E}_{p}[\log q] = \begin{cases}
- \sum_{x \in \mathcal{X}} p(x) \log q(x),& \text{for discrete $p,q$};\\
- \int _{\mathcal{X}}  p(x) \log q(x)\, \mathrm{d}x , &\text{for continuous $p,q$}.
\end{cases}
$$
The cross entropy can be interpreted as the **expected number of bits** needed to encode a data sample drawn from $p$ using a code based on $q$.

```ad-example
Suppose we want to encode a sequence of 4 characters `A, B, C, D` with equal frequency $0.25$. If we encode each character with `0, 10, 110, 111` respectively, the probability of each encoding appearing in a random sequence is $0.5$, $0.25$, $0.125$, and $0.125$. Then the expected number of bits to encode a character with this encoding is:
$$
-\left[ 0.25 \times \log_{2} 0.5 + 0.25 \times \log_{2} 0.25 + 0.25 \times \log_{2} 0.125 + 0.25 \times \log_{2} 0.125 \right] = 2.25. 
$$
```

## Gibb's Inequality

The entropy of a distribution $p$ is less or equal to its cross entropy with **any other distribution** $q$:
$$
H(p) \leq H(p, q),
$$
where the equality holds if and only if $p =q$.

```ad-done
title: Proof

$$
H(p,q) - H(p) =  -\mathbb{E}_{p}[\log q] + \mathbb{E}_{p}[\log p] = \mathbb{E}_{p} \left[ \log \frac{p}{q} \right] = \mathbb{E}_{p} \left[ -\log \frac{q}{p} \right]  .
$$
Since $-\log(x)$ is a **convex function**, using [[Jensen's inequality]],
$$
\mathbb{E}_{p} \left[- \log \frac{q}{p} \right]  \geq -\log \mathbb{E}_{p}\left[ \frac{q}{p} \right] = -\log 1 = 0.
$$
Therefore,
$$
H(p,q) \geq H(p).
$$
```

The difference between $H(p,q)$ and $H(p)$ gives rise to the definition of [[Kullback–Leibler Divergence|KL divergence]].

Using Gibb's inequality, we can also show that the entropy of **uniform categorical distribution** upper bounds the entropy of any [[Bernoulli, Binomial and Categorical Distribution|categorical distribution]].
```ad-done
title: Proof
Given categories $\mathcal{X} = \left\{ C_{1}, \cdots, C_{n} \right\}$ and the uniform distribution $q = \frac{1}{n}$. For any categorical distribution $p$, using Gibb's inequality:
$$
H(p,q) = - \sum_{i=1}^n p(C_{i}) \log \frac{1}{n} = \log n \geq H(p).
$$
```
