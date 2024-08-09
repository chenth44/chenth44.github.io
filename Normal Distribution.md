---
tags:
  - Maths/Probability-Theory
  - Note
aliases:
  - Gaussian Distribution
  - Gaussian
finished-writing: false
---
# Normal Distribution

## Univariate Normal Distribution

### PDF

A **normal distribution** or **Gaussian distribution** is a type of continuous probability distribution for a real-valued random variable. Parameterized by the mean $\mu$ and variance $\sigma^2$, its PDF has the form:
$$
p(x) = \mathcal{N}(x; \mu, \sigma^2) = \frac{1}{\sqrt{ 2 \pi \sigma^2 }} \exp\left(-\frac{(x- \mu)^2}{2\sigma^2}\right).
$$
### Standard Normal

When $\mu = 0$ and $\sigma^2 = 1$, we have the standard normal distribution function with PDF:
$$
\varphi (x) = \frac{1}{\sqrt{ 2\pi }} \exp \left( -\frac{x^2}{2} \right) .
$$

### CDF of a Standard Normal

The CDF of a standard normal distribution has the form:
$$
\Phi(x) = \int_{-\infty}^x \, \varphi(x) \mathrm{d}x = \frac{1}{\sqrt{ 2 \pi }} \int_{-\infty}^x \exp \left( -\frac{x^2}{2} \right)  \, \mathrm{d}x .
$$
- $\Phi(x)$ has **no closed form solution**;
- $\Phi(0) = \frac{1}{2}$;
- $1 - \Phi(x) = \Phi(-x)$.

### Error Function

### Quantile Function

## Multivariate Normal Distribution

### PDF

### Mahalanobis Distance

### Spherical Normal Distribution

