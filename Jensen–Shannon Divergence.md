---  
tags:  
  - Maths/Probability-Theory  
  - Maths/Information-Theory  
  - Note  
aliases:  
  - JS Divergence  
finished-writing: true  
---  
# Jensen-Shannon Divergence  
  
## Definition  
  
The Jensen-Shannon divergence (JS divergence) is defined as:  
$$  
D_ {\text{JS}}(p \mid \mid q) := \frac{1}{2}D_ {\text{KL}} (p \mid \mid m) + \frac{1}{2} D_ {\text{KL}}(q \mid \mid m),  
$$  
where $m = \frac{1}{2}(p + q)$, and $D_ {\text{KL}}$ is the KL divergence.  
  
## Properties  
  
The JS divergence satisfies all properties of a metric space except for the triangle inequality:  
- $D_ {\text{JS}}(p \mid \mid q) \geq 0$, and $D_ {\text{JS}}(p \mid \mid q) = 0$ if and only if $p =q$.  
- $D_ {\text{JS}}(p \mid \mid q) = D_ {\text{JS}}(q \mid \mid p)$.  
  
Additionally, the **square root** of JS divergence also satisfies the triangle inequality and is thus a metric.  
$$  
\sqrt{ D_ {\text{JS}}(p \mid \mid q) } \leq \sqrt{ D_ {\text{JS}}(p \mid \mid r) } + \sqrt{ D_ {\text{JS}}(r \mid \mid q) }.  
$$