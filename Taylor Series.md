---
tags:
  - Maths/Calculus
  - Note
aliases:
  - Taylor expansion
finished-writing: false
---
# Taylor Series

## Multivariate Taylor Series

Given $f:\mathbb{R}^d \to \mathbb{R}$, the Taylor series expanded at $\mathbf{a}$ is:
$$
f(\mathbf{x}) = f(\mathbf{a}) + (\mathbf{x}-\mathbf{a})^{\mathrm{T}} \nabla f(\mathbf{a}) + \frac{1}{2}(\mathbf{x} - \mathbf{a})^{\mathrm{T}} \mathbf{H}(f(\mathbf{a}))(\mathbf{x} - \mathbf{a}) + \cdots,
$$
where $\nabla f(\mathbf{a})$ is the gradient of $f(\mathbf{x})$ evaluated at $\mathbf{a}$, and $\mathbf{H}(f(\mathbf{a}))$ is the [[Hessian Matrix|Hessian Matrix]] evaluated at $\mathbf{a}$.