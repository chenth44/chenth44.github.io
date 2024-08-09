---  
tags:  
  - Maths/Linear-Algebra  
  - Note  
finished-writing: false  
---  
# Quadratic Form  
  
## Definition  
  
A quadratic form is a **polynomial** with terms **all of degree** two:  
$$  
q(x_ {1}, x_ {2}, \cdots, x_ {n}) = \sum_ {i=1}^n \sum_ {j \geq i}^n b_ {ij} x_ {i} x_ {j}.  
$$  
A quadratic form can be also represented by a vector-matrix-vector product:  
$$  
q(\mathbf{x}) = \mathbf{x}^{\mathrm{T}}\mathbf{A}\mathbf{x},  
$$  
where  
$$  
\mathbf{A} = \begin{bmatrix}  
a_ {11} & \cdots & a_ {1n} \\  
\vdots & \ddots & \vdots \\  
a_ {n 1} & \cdots & a_ {nn}  
\end{bmatrix},  
$$  
and $a_ {ij} + a_ {ji} = b_ {ij}$.  
  
Therefore, a quadratic form can be represented by a **unique** symmetric matrix $\mathbf{A}$, where $a_ {ij} = a_ {ji} = \frac{b_ {ij}}{2}$, or a **unique** triangular matrix $\mathbf{A}$, where  
$$  
\mathbf{A} = \begin{bmatrix}  
b_ {11} & b_ {12} & \cdots & b_ {1 n} \\  
 & b_ {22} & \cdots & b_ {2n} \\  
 &  & \ddots & \vdots \\  
 &  &  & b_ {n n}  
\end{bmatrix}.  
$$  
## Definiteness of Quadratic Forms  
  
  
  
## Geometry of Quadratic Forms  
  
  
## Eigenvectors Optimize Quadratic Forms  
  
## Generalized Quadratic Form  
  
The quadratic form can be generalized as  
$$  
f(\mathbf{x}) = \alpha\mathbf{x}^{\mathrm{T}}\mathbf{A}\mathbf{x} + \beta\mathbf{b}^{\mathrm{T}}\mathbf{x} + c.  
$$  
Using completing the square, we can convert this form to a quadratic form plus a constant:  
$$  
\mathbf{x}^{\mathrm{T}}\mathbf{A} \mathbf{x} - 2\mathbf{b}^{\mathrm{T}}\mathbf{x} = (\mathbf{x} - \mathbf{A}^{-1}\mathbf{b})^{\mathrm{T}} \mathbf{A} (\mathbf{x} - \mathbf{A}^{-1} \mathbf{b}) - \mathbf{b}^{\mathrm{T}} \mathbf{A}^{-1} \mathbf{b}.  
$$  
More generally,  
$$  
\alpha  \mathbf{x}^{\mathrm{T}} \mathbf{A} \mathbf{x} + \beta \mathbf{b}^{\mathrm{T}} \mathbf{x} + c = \left( \mathbf{x} + \frac{\beta}{2\alpha} \mathbf{A}^{-1}\mathbf{b} \right)^{\mathrm{T}} \mathbf{A} \left( \mathbf{x} +\frac{\beta}{2\alpha}\mathbf{A}^{-1} \mathbf{b} \right) - \frac{\beta^2}{4\alpha} \mathbf{b}^{\mathrm{T}} \mathbf{A}^{-1} \mathbf{b} + c.  
$$  
