---
tags:
  - Maths/Probability-Theory
  - Maths/Information-Theory
  - Note
aliases:
  - KL Divergence
finished-writing: true
---
# Kullback-Leiber Divergence

## Definition

For two distributions $p$ and $q$, the Kullback-Leiber divergence (KL divergence) is defined as
$$
D_{\text{KL}} (p \mid \mid q) := H(p,q) - H(p) = \mathbb{E}_{p}\left[ \log \frac{p}{q} \right].
$$
The KL divergence can be used to measure how much $p$ differs from $q$.
## Properties

- From [[Entropy#Gibb's Inequality|Gibb's inequality]], $D_{\text{KL}}(p \mid \mid q) \geq 0$, and the equality holds if and only if $p =q$.
- The KL divergence is additive for **independent** distributions $p_{1} \perp p_{2}$ and $q_{1} \perp q_{2}$: $$ D_{\text{KL}}(p_{1} \mid \mid q_{1}) + D_{\text{KL}}(p_{2} \mid \mid q_{2}) = D_{\text{KL}}(p_{1}p_{2} \mid \mid q_{1}q_{2}).$$

## KL divergence is not a Metric Distance

KL divergence is not a [[Metric Space|metric distance]], because it does not satisfies the symmetric axiom and the triangle inequality. [[Jensen–Shannon Divergence|Jensen–Shannon Divergence]], or JS divergence, is a symmetrized version of it.
