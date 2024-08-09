---  
tags:  
  - Machine-Learning/Deep-Learning/Generative-Model/Diffusion-Model  
  - Note  
finished-writing: true  
share: true  
title: 2024-08-09-diffusion-models  
links: false  
---  
  
# Diffusion Models  
  
Original paper: Denoising Diffusion Probabilistic Models  
Survey: Understanding Diffusion Models A Unified Perspective  
## Intuition  
  
A diffusion model is trained through a diffusion process that progressively adds noise to the original data. The model then learns how to reconstruct the original data from this noisy input.  
  
```mermaid  
graph LR; A[Original Data] -->|Forward Diffusion #40;Fixed#41;| B[Noisy Data]; B -->|Reverse Denoising #40;Learned#41;| A; style A fill:#f9f,stroke:#333,stroke-width:2px style B fill:#ccf,stroke:#333,stroke-width:2px  
```  
  
Once the model has sufficiently learned to reconstruct the data distribution from a typically Gaussian noise distribution, it gains the capability to generate new, novel data.  
  
## Forward Process  
  
At each time step $t$, the transition probability from input $\mathbf{x}\_{t-1}$ to output $\mathbf{x}\_{t}$ is defined as a Gaussian:  
$$  
q(\mathbf{x}\_{t} \mid \mathbf{x}\_{t-1}) = \mathcal{N}(\sqrt{ 1 - \beta\_{t} } \mathbf{x}\_{t-1}, \beta\_{t} \mathbf{I}),  
$$  
where $\beta\_{t} \in (0,1)$.  
  
### Reparameterization  
  
We can generate $\mathbf{x}\_{t}$ using the reparameterization trick:  
$$  
\mathbf{x}\_{t} = \sqrt{ 1 - \beta\_{t} } \mathbf{x}\_{t-1} + \sqrt{ \beta\_{t} } \boldsymbol{\epsilon}\_{t},\; \boldsymbol{\epsilon}\_{t} \sim \mathcal{N}(\mathbf{0}, \mathbf{I}).  
$$  
  
Furthermore, we can generate $\mathbf{x}\_{t}$ from $\mathbf{x}\_{0}$ with one reparameterization step:  
$$  
\mathbf{x}\_{t} = \sqrt{ \bar{\alpha}\_{t} } \mathbf{x}\_{0} + \sqrt{ 1 - \bar{\alpha}\_{t} } \boldsymbol{\epsilon},  
$$  
where  
$$  
\bar{\alpha}\_{t} := \prod\_{i=1}^t \alpha\_{i},\; \alpha\_{i} = 1-\beta\_{i},\; \boldsymbol{\epsilon} \sim \mathcal{N}(\mathbf{0}, \mathbf{I}).  
$$  
```ad-done  
title: Proof  
collapse: close  
  
Define $\alpha_{t} = 1- \beta_{t}$,  
$$  
\begin{align}  
\mathbf{x}_{t} &= \sqrt{ \alpha_{t} } \mathbf{x}_{t - 1} + \sqrt{ \beta_{t} } \boldsymbol{\epsilon}_{t}  \\  
&= \sqrt{ \alpha_{t} } (\sqrt{ \alpha_{t-1} } \mathbf{x}_{t-2} + \sqrt{ \beta_{t - 1} } \boldsymbol{\epsilon}_{t-1}) + \sqrt{ \beta_{t} } \boldsymbol{\epsilon}_{t} \\  
&= \sqrt{ \alpha_{t} } \left( \sqrt{ \alpha_{t-1} } \left( \sqrt{ \alpha_{t-2}  } \mathbf{x}_{t-3} + \sqrt{ \beta_{t-2} } \boldsymbol{\epsilon}_{t-2} \right) + \sqrt{ \beta_{t-1} } \boldsymbol{\epsilon}_{t-1}  \right) + \sqrt{ \beta_{t} } \boldsymbol{\epsilon}_{t} \\  
&= \cdots \\  
&= \left( \prod_{i=\tau}^t \sqrt{ \alpha_{i} }  \right) \mathbf{x}_{\tau -1} + \sum_{i= \tau}^t \left( \prod_{j = i + 1}^t \sqrt{ \alpha_{j} } \right) \sqrt{ \beta_{i} } \boldsymbol{\epsilon}_{i}.  
\end{align}  
$$  
  
The second term is the sum of i.i.d. zero-mean Gaussians, and is thus also a zero-mean [[Normal Distribution|Gaussian]], with variance  
$$  
\begin{align}  
\sum_{i= \tau}^t \left( \prod_{j=i + 1}^t \alpha_{j} \right) \beta_{i} &= \sum_{i= \tau}^t \underbrace{ \left( \prod_{j=i + 1}^t \alpha_{j} \right)  }_{ \Pi_{i+1 : t} }(1 - \alpha_{i}) \\  
&= \sum_{i=\tau}^t \Pi_{i+1:t} - \Pi _{i:t} \\  
&= \cancel{ \Pi_{\tau + 1 : t} } - \Pi_{\tau : t} + \cancel{ \Pi_{\tau + 2 : t} } \cancel{ - \Pi_{\tau + 1 : t} }  + \cdots + 1 \cancel{ - \Pi_{t:t} } \\  
&= 1- \Pi_{\tau : t} = 1 - \prod_{j = \tau}^t \alpha_{j}  
\end{align}  
$$  
Therefore, the forward diffusion process from time step $\tau$ to $t$ can be done with only one reparameterization  
$$  
\mathbf{x}_{t} = \left( \prod_{i=\tau}^t \sqrt{ \alpha_{i} } \right) \mathbf{x}_{\tau - 1} + \sqrt{ 1 - \prod_{i=\tau}^t \alpha_{i} }  \boldsymbol{\epsilon},   
$$  
where $\boldsymbol{\epsilon} \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$.  
```  
### Asymptotics  
  
Since $\alpha\_{i} \in \left( 0, 1 \right)$, as $t \to \infty$  
$$  
\bar{\alpha}\_{t} \to 0.  
$$  
Therefore, regardless of $\mathbf{x}\_{0}$,  
$$  
\mathbf{x}\_{\infty} \sim \mathcal{N}(\mathbf{0}, \mathbf{I}).  
$$  
## Reverse Process  
  
The posterior distribution, $q(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{t})$, is generally **intractable**:  
$$  
q(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{t}) = \frac{q(\mathbf{x}\_{t} \mid \mathbf{x}\_{t-1})q(\mathbf{x}\_{t-1})}{q(\mathbf{x}\_{t})},  
$$  
where  
$$  
q(\mathbf{x}\_{t}) = \int \_{\mathbf{x}\_{0} \in \mathcal{X}} q(\mathbf{x}\_{t} \mid \mathbf{x}\_{0}) q(\mathbf{x}\_{0}) \, \mathrm{d}\mathbf{x}\_{0}.   
$$  
  
Therefore, we learn a network parameterized by $\theta$ to approximate $q(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{t})$ with a Gaussian:  
$$  
p\_{\theta}(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{t}) := \mathcal{N}(\boldsymbol{\mu}\_{\theta}(\mathbf{x}\_{t}, t), \boldsymbol{\Sigma}\_{\theta}(\mathbf{x}\_{t}, t)).  
$$  
  
We approximate the log-likelihood using the ELBO:  
$$  
\begin{align}  
-\log p\_{\theta}(\mathbf{x}\_{0})  &=& &-\mathbb{E}\_{q(\mathbf{x}\_{0})} \left[ \log p\_{\theta}(\mathbf{x}\_{0}) \right]  \\  
&\leq &&-\mathbb{E}\_{q} \left[ \log \frac{p\_{\theta}(\mathbf{x}\_{0 : T})}{q(\mathbf{x}\_{1:T} \mid \mathbf{x}\_{0})}  \right]  \\  
&=  && \mathbb{E}\_{q} [ \underbrace{ D\_{\text{KL}}(q(\mathbf{x}\_{T} \mid \mathbf{x}\_{0})  \mid \mid p\_{\theta}(\mathbf{x}\_{T})) }\_{ \mathcal{L}\_{T} }  \\  
&&&  + \underbrace{ \sum\_{t>1}^T D\_{\text{KL}}(q(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{t}, \mathbf{x}\_{0}) \mid \mid p\_{\theta}(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{t})) }\_{ \mathcal{L}\_{1 :T-1} }  \\  
&&& \underbrace{ - \log p\_{\theta}(\mathbf{x}\_{0} \mid \mathbf{x}\_{1}) }\_{ \mathcal{L}\_{0} } ].  
\end{align}  
$$  
````ad-done  
title: Derivation  
collapse: close  
$$  
\begin{align}  
\log \frac{p_{\theta}(\mathbf{x}_{0 : T})}{q(\mathbf{x}_{1:T} \mid \mathbf{x}_{0})} &= \log \frac{p_{\theta}(\mathbf{x}_{T}) \prod_{t=1}^T p_{\theta}(\mathbf{x}_{t-1} \mid \mathbf{x}_{t})}{\prod_{t=1}^T q(\mathbf{x}_{t} \mid \mathbf{x}_{t-1})} \\  
&= \log p_{\theta}(\mathbf{x}_{T}) + \sum_{t=1}^T \log \frac{p_{\theta}(\mathbf{x}_{t-1} \mid \mathbf{x}_{t})}{q(\mathbf{x}_{t} \mid \mathbf{x}_{t-1})} \\  
&= \log p_{\theta}(\mathbf{x}_{T}) + \sum_{t > 1}^T \log \frac{p_{\theta}(\mathbf{x}_{t-1} \mid \mathbf{x}_{t})}{q(\mathbf{x}_{t} \mid \mathbf{x}_{t-1})} + \log \frac{p_{\theta}(\mathbf{x}_{0} \mid \mathbf{x}_{1}) }{q(\mathbf{x}_{1} \mid \mathbf{x}_{0})}  
\end{align}  
$$  
```ad-attention  
Here $q(\mathbf{x}\_t \mid \mathbf{x}\_{t-1})$ is not tractable since $\mathbf{x}\_{t-1}$ is unknown.  
```  
  
Since $\mathbf{x}_{0:T}$ is a [[Markov Chains|Markov Chain]], for $t > 1$,  
$$  
\begin{align}  
& \mathbf{x}_{t} \perp \mathbf{x}_{0:t-2} \mid \mathbf{x}_{t-1}  \\  
\implies &q(\mathbf{x}_{t} \mid \mathbf{x}_{t-1} ) = q(\mathbf{x}_{t} \mid \mathbf{x}_{t-1}, \mathbf{x}_{0}) = \frac{q(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, \mathbf{x}_{0})q(\mathbf{x}_{t} \mid \mathbf{x}_{0})}{q(\mathbf{x}_{t-1} \mid \mathbf{x}_{0})}.  
\end{align}  
$$  
Plugging in,  
$$  
\begin{align}  
\log \frac{p_{\theta}(\mathbf{x}_{0 : T})}{q(\mathbf{x}_{1:T} \mid \mathbf{x}_{0})} &=&& \log p_{\theta}(\mathbf{x}_{T}) + \sum_{t > 1}^T \log \frac{p_{\theta}(\mathbf{x}_{t-1} \mid \mathbf{x}_{t})}{q(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, \mathbf{x}_{0})}\frac{q(\mathbf{x}_{t-1} \mid \mathbf{x}_{0})}{q(\mathbf{x}_{t} \mid \mathbf{x}_{0})}  \\  
&&&+ \log \frac{p_{\theta}(\mathbf{x}_{0} \mid \mathbf{x}_{1}) }{q(\mathbf{x}_{1} \mid \mathbf{x}_{0})} \\  
&=&& \log \frac{p_{\theta}(\mathbf{x}_{T})}{q(\mathbf{x}_{T} \mid \mathbf{x}_{0})} + \sum_{t>1}^T \log \frac{p_{\theta}(\mathbf{x}_{t-1} \mid \mathbf{x}_{t})}{q(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, \mathbf{x}_{0})}  \\  
&&&+ \log p_{\theta}(\mathbf{x}_{0} \mid \mathbf{x}_{1}).  
\end{align}  
$$  
````  
  
### The $\mathcal{L}_{T}$ Term  
  
Since $\mathbf{x}_{T}$ is directly sampled from a standard [[Normal Distribution|Gaussian distribution]], $\mathcal{L}_{T}$ has no learnable parameters for fixed $\beta_{1:T}$, thus **ignored** from the training objective.  
  
### The $\mathcal{L}_{1:T-1}$ Term  
  
The posterior conditioned on $\mathbf{x}_{0}$, $q(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, \mathbf{x}_{0})$, is a tractable [[Normal Distribution|Gaussian]]:  
$$  
q(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, \mathbf{x}_{0}) = \mathcal{N}(\tilde{\boldsymbol{\mu}}_{t}(\mathbf{x}_{t}, \mathbf{x}_{0}), \tilde{\beta}_{t} \mathbf{I}),  
$$  
where  
$$  
\begin{align}  
\tilde{\boldsymbol{\mu}}_{t}(\mathbf{x}_{t}, \mathbf{x}_{0}) &:=  \frac{1}{\sqrt{ \alpha_{t} }}\mathbf{x}_{t} - \frac{1-\alpha_{t}}{\sqrt{ 1 - \bar{\alpha}_{t} } \sqrt{ \alpha_{t} }} \boldsymbol{\epsilon}; \\  
\tilde{\beta}_{t} &:= \frac{1-\bar{\alpha}_{t-1}}{1- \bar{\alpha}_{t}} \beta_{t}.  
\end{align}  
$$  
  
```ad-done  
title: Proof  
collapse: close  
Using Bayes' Theorem,  
$$  
\begin{align}  
q(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{t}, \mathbf{x}\_{0}) &\propto  q(\mathbf{x}\_{t} \mid \mathbf{x}\_{t-1}, \mathbf{x}\_{0}) q(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{0}) \\  
&\propto \exp \left( -\frac{1}{2} \frac{\lVert \mathbf{x}\_{t} - \sqrt{ 1 - \beta\_{t}  } \mathbf{x}\_{t-1} \rVert^2 }{\beta\_{t}} \right) \cdot \exp \left( -\frac{1}{2} \frac{\lVert \mathbf{x}\_{t-1} - \sqrt{ \bar{\alpha}\_{t-1}  } \mathbf{x}\_{0} \rVert^2 }{1- \bar{\alpha}\_{t-1}} \right)  \\  
&= \exp \left[ -\frac{1}{2} \left( \frac{\lVert \mathbf{x}\_{t} - \sqrt{ 1 - \beta\_{t}  } \mathbf{x}\_{t-1} \rVert^2 }{\beta\_{t}} + \frac{\lVert \mathbf{x}\_{t-1} - \sqrt{ \bar{\alpha}\_{t-1}  } \mathbf{x}\_{0} \rVert^2 }{1- \bar{\alpha}\_{t-1}} \right)  \right] .  
\end{align}  
$$  
The coefficient of $\mathbf{x}\_{t-1}^2$ is  
$$  
\frac{1}{\tilde{\beta}\_{t}} = \frac{1-\beta\_{t}}{\beta\_{t}} + \frac{1}{1- \bar{\alpha}\_{t-1}} = \frac{1 - \bar{\alpha}\_{t}}{(1 - \bar{\alpha}\_{t-1})\beta\_{t}}.  
$$  
Therefore,  
$$  
\tilde{\beta}\_{t} = \frac{1 - \bar{\alpha}\_{t-1}}{1- \bar{\alpha}\_{t}} \beta\_{t}.  
$$  
The coefficient of $\mathbf{x}\_{t-1}$ is  
$$  
-\frac{2\tilde{\boldsymbol{\mu}}\_{t}}{\tilde{\beta}\_{t}} =-\frac{2\sqrt{ 1-\beta\_{t} }\mathbf{x}\_{t}}{\beta\_{t}} - \frac{2\sqrt{ \bar{\alpha}\_{t-1} }\mathbf{x}\_{0}}{1- \bar{\alpha}\_{t-1}} = -2 \left( \frac{(1 - \bar{\alpha}\_{t-1})\sqrt{ \alpha\_{t} }\mathbf{x}\_{t} + \beta\_{t} \sqrt{ \bar{\alpha}\_{t-1}  }\mathbf{x}\_{0}}{\beta\_{t}(1 - \bar{\alpha}\_{t-1})} \right) .  
$$  
Plugging $\tilde{\beta}\_{t}$,  
$$  
\begin{align}  
\tilde{\boldsymbol{\mu}}\_{t} &= \left( \frac{(1 - \bar{\alpha}\_{t-1})\sqrt{ \alpha\_{t} }\mathbf{x}\_{t} + \beta\_{t} \sqrt{ \bar{\alpha}\_{t-1}  }\mathbf{x}\_{0}}{\beta\_{t}(1 - \bar{\alpha}\_{t-1})} \right) \tilde{\beta}\_{t} \\  
&= \frac{(1- \bar{\alpha}\_{t-1})\sqrt{ \alpha\_{t} }}{1 - \bar{\alpha}\_{t}}\mathbf{x}\_{t} + \frac{\beta\_{t} \sqrt{ \bar{\alpha}\_{t-1}  }}{1 - \bar{\alpha}\_{t}} \mathbf{x}\_{0} \\  
&= \frac{(1- \bar{\alpha}\_{t-1})\sqrt{ \alpha\_{t} }}{1 - \bar{\alpha}\_{t}}\mathbf{x}\_{t} + \frac{\beta\_{t} \sqrt{ \bar{\alpha}\_{t-1}  }}{1 - \bar{\alpha}\_{t}} \frac{ \mathbf{x}\_{t} - \sqrt{ 1 - \bar{\alpha}\_{t} } \boldsymbol{\epsilon} }{\sqrt{ \bar{\alpha}\_{t} }}  \\  
&=  \frac{1}{\sqrt{ \alpha\_{t} }} \mathbf{x}\_{t} - \frac{1- \alpha\_{t}}{\sqrt{ 1 - \bar{\alpha}\_{t} }\sqrt{ \alpha\_{t} }} \boldsymbol{\epsilon}  
\end{align}  
$$  
```  
  
For simplicity, $\boldsymbol{\Sigma}_{\theta}(\mathbf{x}_{t}, t)$ is set to untrained time-dependent constants $\sigma_{t}^2 \mathbf{I}$, where $\sigma_{t}^2 = \tilde{\beta}_{t}$. Since $q(\mathbf{x}_{t -1} \mid \mathbf{x}_{t}, \mathbf{x}_{0})$ and $p_{\theta}(\mathbf{x}_{t-1} \mid \mathbf{x}_{t})$ are both tractable Gaussians, the [[Kullback–Leibler Divergence|KL divergence]] has a closed form solution (see [[Kullback–Leibler Divergence|KL divergence between Gaussians]]):  
$$  
\mathcal{L}_{t-1} = \frac{1}{2\sigma_{t}^2} \lVert \tilde{\boldsymbol{\mu}}_{t}(\mathbf{x}_{t}, \mathbf{x}_{0}) - \boldsymbol{\mu}_{\theta}(\mathbf{x}_{t}, t) \rVert^2 + C .  
$$  
  
### The $\mathcal{L}_{0}$ Term  
  
To obtain **discrete log-likelihood**, this term is set to an **independent discrete decoder** derived from [[Normal Distribution|Gaussian]] $\mathcal{N}(\mathbf{x}_{0}; \boldsymbol{\mu}_{\theta}(\mathbf{x}_{1}, 1), \sigma_{1}^2 \mathbf{I})$. For discrete $\mathbf{x}_{t}$ of dimension $D$,  
$$  
p_{\theta}(\mathbf{x}_{0} \mid \mathbf{x}_{1}) = \prod_{i=1}^D \int_{F(x) = (\mathbf{x}_{0})_{i} } \mathcal{N}(x; (\mu_{\theta})_{i}, \sigma_{1}^2 )  \, \mathrm{d}x,   
$$  
where $F(x)$ is a discretization function that maps $x \in \mathbb{R}$ into discrete $(\mathbf{x}_{0})_{i}$.  
  
## Training  
  
The formulation of $\tilde{\boldsymbol{\mu}}_{t}(\mathbf{x}_{t}, \mathbf{x}_{0})$ indicates that instead of directly predicting a mean $\boldsymbol{\mu}_{\theta}(\mathbf{x}_{t},t)$, it is better to predict a $\boldsymbol{\epsilon}_{\theta}(\mathbf{x}_{t}, t)$, such that  
$$  
\mathcal{L}_{t-1} - C \propto \lVert \boldsymbol{\epsilon} - \boldsymbol{\epsilon}_{\theta}(\mathbf{x}_{t}, t) \rVert ^2.  
$$  
Combining with the reparameterization step, we have the training objective:  
$$  
\lVert \boldsymbol{\epsilon} - \boldsymbol{\epsilon}_{\theta}(\sqrt{ \bar{\alpha}_{t} }\mathbf{x}_{0} + \sqrt{ 1 - \bar{\alpha}_{t} }\boldsymbol{\epsilon}) \rVert^2.   
$$  
  
We thus have the training algorithm:  
```pseudo  
\begin{algorithm}  
\caption{Training}  
\begin{algorithmic}  
\Repeat  
    \State $\mathbf{x}\_0 \sim q(\mathbf{x}\_0)$  
    \State $t \sim \text{Uniform}(\{1, \ldots, T\})$  
    \State $\boldsymbol{\epsilon} \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$  
    \State Take gradient descent step on $\nabla\_\theta \| \boldsymbol{\epsilon} - \boldsymbol{\epsilon}\_\theta (\sqrt{\bar\alpha\_t} \mathbf{x}\_0 + \sqrt{1-\bar\alpha\_t} \boldsymbol{\epsilon}, t) \|^2$  
\Until{converged}  
\end{algorithmic}  
\end{algorithm}  
```  
  
## Sampling  
  
The sampling algorithm also uses [[Reparameterization Trick|the reparameterization trick]] to sample $\mathbf{x}_{t-1}$ given $\mathbf{x}_{t}$:  
$$  
\mathbf{x}_{t - 1} = \frac{1}{\sqrt{ \alpha_{t} }} \left( \mathbf{x}_{t} - \frac{1-\alpha_{t}}{\sqrt{ 1-\bar{\alpha}_{t} }}\boldsymbol{\epsilon}_{\theta}(\mathbf{x}_{t}, t) \right) +\sigma_{t} \mathbf{z},  
$$  
where $\mathbf{z} \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$ if $t > 1$, and $\mathbf{z}$ is set to zero when $t=1$ to generate deterministic results.  
  
```pseudo  
  
\begin{algorithm}  
\caption{Sampling}  
\begin{algorithmic}  
\State $\mathbf{x}\_T \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$  
\For{$t = T, \cdots, 1$}  
    \State $\mathbf{z} \sim \begin{cases}   
    \mathcal{N}(\mathbf{0}, \mathbf{I}) & \text{if } t > 1 \\  
    \mathbf{0} & \text{otherwise}  
    \end{cases}$  
    \State $\mathbf{x}\_{t-1} = \frac{1}{\sqrt{\alpha\_t}} \left(\mathbf{x}\_t - \frac{1-\alpha\_t}{\sqrt{1-\bar\alpha\_t}} \boldsymbol\epsilon\_\theta(\mathbf{x}\_t, t)\right) + \sigma\_t \mathbf{z}$  
\EndFor  
\State \Return $\mathbf{x}\_0$  
\end{algorithmic}  
\end{algorithm}  
  
```  
  
## Network Architecture  
  
The network for estimating $\boldsymbol{\epsilon}\_{\theta}(\mathbf{x}\_{t}, t)$ is typically a U-Net architecture, where parameters are shared across time steps, and the time step $t$ is taken as input.  
  
## Extensions and Applications  
  
### Conditional Generation  
  
- Additional conditioning information $y$ is applied to control generation.  
- The network now samples $\mathbf{x}\_{t-1}$ from $p\_{\theta}(\mathbf{x}\_{t-1} \mid \mathbf{x}\_{t}, t, y)$.  
- The condition $y$ is taken as input in the similar fashion to $t$.  
- ControlNet  
  
### Guidance Methods  
  
- Gradient guidance added to shift the predicted mean.  
- For more information, see Guidance Methods.  
  
### Latent Diffusion Models  
  
- Train a VAE to map the input data to latent space.  
- Diffusion and denoising are performed within the latent space.  
- Lower computational cost.