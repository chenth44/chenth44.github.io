---  
tags:  
  - Machine-Learning/Deep-Learning/Generative-Model/Diffusion-Model  
  - Note  
finished-writing: false  
---  
# Guidance Methods  
  
## Intuition  
  
In the vanilla conditional diffusion model, the conditioning information $y$ is directly taken as input to the model:  
$$  
\begin{align*}  
\mathbf{x}_ {t-1} &\sim p_ {\theta}(\mathbf{x}_ {t-1} \mid \mathbf{x}_ {t}, t, y), \\  
\mathcal{L} &= \lVert \boldsymbol{\epsilon} - \hat{\boldsymbol{\epsilon}}_ {\theta}(\mathbf{x}_ {t}, t, y) \rVert^2 .  
\end{align*}  
$$  
However, the model tends to **forget or downplay** any given conditioning information, because it can still reconstruct the input without the conditioning information.  
  
## Classifier Guidance  
  
See Score-Based Generative Modeling through Stochastic Differential Equations and Diffusion Models Beat GANs on Image Synthesis.  
  
The conditional posterior distribution can be approximated by:  
$$  
p_ {\theta, \phi}(\mathbf{x}_ {t-1} \mid \mathbf{x}_ {t}, y) = Zp_ {\theta}(\mathbf{x}_ {t-1} \mid \mathbf{x}_ {t}) p_ {\phi}(y \mid \mathbf{x}_ {t-1}),  
$$  
where $Z$ is a normalizing constant, $p_ {\theta}(\mathbf{x}_ {t-1} \mid \mathbf{x}_ {t})$ is the **trained** unconditional posterior distribution as in the vanilla diffusion model, and $p_ {\phi}(y \mid \mathbf{x}_ {t})$ is a **classifier**.  
  
```ad-done  
title: Proof  
$$  
\begin{align}  
\hat{q}(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, y) &= \frac{\hat{q}(\mathbf{x}_{t-1}, \mathbf{x}_{t}, y)}{\hat{q}(\mathbf{x}_{t}, y)} \\  
&= \frac{\hat{q}(y \mid \mathbf{x}_{t-1}, \mathbf{x}_{t})\hat{q}(\mathbf{x}_{t-1} \mid \mathbf{x}_{t})\cancel{ \hat{q}(\mathbf{x}_{t}) }}{\hat{q}(y \mid \mathbf{x}_{t}) \cancel{ \hat{q}(\mathbf{x}_{t}) }} \\  
&= \frac{\overbrace{ \hat{q}(y \mid \mathbf{x}_{t-1}) }^{ \text{by } (* *) } \cdot \overbrace{ q(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}) }^{ \text{by }(*) }}{\underbrace{ \hat{q}(y \mid \mathbf{x}_{t}) }_{ \text{constant} }}  \\  
&= Z q(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}) \hat{q}(y \mid \mathbf{x}_{t-1})  
\end{align}  
$$  
```  
  
Recall that the log-likelihood of the unconditional posterior is  
$$  
\log p_ {\theta}(\mathbf{x}_ {t-1} \mid \mathbf{x}_ {t}) = -\frac{1}{2} (\mathbf{x}_ {t-1} - \boldsymbol{\mu})^{\mathrm{T}} \boldsymbol{\Sigma}^{-1}(\mathbf{x}_ {t-1} - \boldsymbol{\mu}) + C.  
$$  
  
We can approximate $\log p_ {\phi}(y \mid \mathbf{x}_ {t-1})$ using a Taylor expansion around $\boldsymbol{\mu}$:  
$$  
\begin{align*}  
\log p_ {\phi}(y \mid \mathbf{x}_ {t-1}) &\approx \underbrace{ \log p_ {\phi}(y \mid \mathbf{x}_ {t-1})\rvert_ {\mathbf{x}_ {t-1} = \boldsymbol{\mu}} }_ { C_ {1} } + (\mathbf{x}_ {t-1} - \boldsymbol{\mu})^{\mathrm{T}} \underbrace{ \nabla_ {\mathbf{x}_ {t-1}} \log p_ {\phi}(y \mid \mathbf{x}_ {t-1})\rvert_ {\mathbf{x}_ {t-1} = \boldsymbol{\mu}} }_ { \mathbf{g} } \\  
&= (\mathbf{x}_ {t-1} - \boldsymbol{\mu})^{\mathrm{T}} \mathbf{g} + C_ {1}.  
\end{align*}  
$$  
Therefore, using completing the square,  
$$  
\begin{align*}  
\log p_ {\theta, \phi}(\mathbf{x}_ {t-1} \mid \mathbf{x}_ {t}, y) &\approx -\frac{1}{2} (\mathbf{x}_ {t-1} - \boldsymbol{\mu})^{\mathrm{T}} \boldsymbol{\Sigma}^{-1}(\mathbf{x}_ {t-1} - \boldsymbol{\mu}) + (\mathbf{x}_ {t-1} - \boldsymbol{\mu})^{\mathrm{T}} \mathbf{g} + C_ {2} \\  
&= -\frac{1}{2}(\mathbf{x}_ {t-1} - \boldsymbol{\mu} - \boldsymbol{\Sigma}\mathbf{g})^{\mathrm{T}} \boldsymbol{\Sigma}^{-1}  (\mathbf{x}_ {t-1} - \boldsymbol{\mu} - \boldsymbol{\Sigma}\mathbf{g}) + \frac{1}{2}\mathbf{g}^{\mathrm{T}} \boldsymbol{\Sigma} \mathbf{g} + C_ {2} \\  
&= -\frac{1}{2}(\mathbf{x}_ {t-1} - \boldsymbol{\mu} - \boldsymbol{\Sigma}\mathbf{g})^{\mathrm{T}} \boldsymbol{\Sigma}^{-1}  (\mathbf{x}_ {t-1} - \boldsymbol{\mu} - \boldsymbol{\Sigma}\mathbf{g}) + C_ {3} \\  
&= \log p(\mathbf{z}) + C_ {4},\; \mathbf{z} \sim \mathcal{N}(\boldsymbol{\mu} + \boldsymbol{\Sigma}\mathbf{g}, \boldsymbol{\Sigma}).  
\end{align*}  
$$  
The conditional transition operator can be approximated by a Gaussian distribution similar to the unconditional transition operator, but with the mean shifted by $\boldsymbol{\Sigma}\mathbf{g}$, where $\mathbf{g}$ is the gradient of the classifier evaluated at $\boldsymbol{\mu}$.  
  
### Sampling Algorithm (DDPM)  
```pseudo  
\begin{algorithm}  
\caption{Sampling (DDPM)}  
\begin{algorithmic}  
\Input class label $y$, gradient scale $s$  
\State $\mathbf{x}_T \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$  
\For{$t = T, \cdots, 1$}  
    \State $\boldsymbol{\mu},\;\mathbf{\Sigma} \gets \boldsymbol{\mu}_\theta(\mathbf{x}_t, t),\;\mathbf{\Sigma}_{\theta}(\mathbf{x}_t, t)$  
    \State $\mathbf{x}_{t-1} \sim \mathcal{N}(\boldsymbol{\mu} + s\mathbf{\Sigma}\nabla_{\mathbf{x}_{t} }\log p_{\phi}(y \mid \mathbf{x}_{t}, t), \mathbf{\Sigma})$  
\EndFor  
\State \Return $\mathbf{x}_0$  
\end{algorithmic}  
\end{algorithm}  
```  
### Sampling Algorithm (DDIM)  
  
## Classifier-free Guidance  
  
See Classifier-Free Diffusion Guidance.  
