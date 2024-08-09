---  
tags:  
  - Note  
  - Maths/Topology  
aliases:  
  - Metric  
finished-writing: false  
---  
# Metric Space  
  
A metric space is an **ordered pair** $(M, d)$ where $M$ is a set and $d$ is a metric on $M$:  
$$  
d: M \times M \to \mathbb{R}.  
$$  
A metric should satisfy the following axioms for all $x,y,z \in M$:  
- $d(x,x) = 0$.  
- **Positivity**: $d(x,y) \geq 0$ if $x \neq y$.  
- **Symmetric**: $d(x,y) = d(y,x)$.  
- **Triangle inequality**: $d(x,z) \leq d(x,y) + d(y,z)$.