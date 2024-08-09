---  
tags:  
  - Paper  
  - Machine-Learning/Deep-Learning/Generative-Model/Diffusion-Model  
aliases:   
citekey: songScoreBasedGenerativeModeling2021  
authors:  
  - Yang Song  
  - Jascha Sohl-Dickstein  
  - Diederik P. Kingma  
  - Abhishek Kumar  
  - Stefano Ermon  
  - Ben Poole  
date: 2021-02-10  
collections:  
  - Diffusion-Models  
local-library: zotero://select/items/1_U4D7DEGQ  
cloud-library: http://zotero.org/users/10051742/items/U4D7DEGQ  
comment:   
finished-reading: false  
---  
  
  
# Score-Based Generative Modeling through Stochastic Differential Equations  
  
## Abstract  
  
Creating noise from data is easy; creating data from noise is generative modeling. We present a stochastic differential equation (SDE) that smoothly transforms a complex data distribution to a known prior distribution by slowly injecting noise, and a corresponding reverse-time SDE that transforms the prior distribution back into the data distribution by slowly removing the noise. Crucially, the reverse-time SDE depends only on the time-dependent gradient ﬁeld (a.k.a., score) of the perturbed data distribution. By leveraging advances in score-based generative modeling, we can accurately estimate these scores with neural networks, and use numerical SDE solvers to generate samples. We show that this framework encapsulates previous approaches in score-based generative modeling and diffusion probabilistic modeling, allowing for new sampling procedures and new modeling capabilities. In particular, we introduce a predictor-corrector framework to correct errors in the evolution of the discretized reverse-time SDE. We also derive an equivalent neural ODE that samples from the same distribution as the SDE, but additionally enables exact likelihood computation, and improved sampling efﬁciency. In addition, we provide a new way to solve inverse problems with score-based models, as demonstrated with experiments on class-conditional generation, image inpainting, and colorization. Combined with multiple architectural improvements, we achieve record-breaking performance for unconditional image generation on CIFAR-10 with an Inception score of 9.89 and FID of 2.20, a competitive likelihood of 2.99 bits/dim, and demonstrate high ﬁdelity generation of 1024 ˆ 1024 images for the ﬁrst time from a score-based generative model.  
  
  
## Introduction  
  
## Related Work  
  
## Methods  
  
## Results and Discussions  
  
```ad-seealso  
title: Also see  
URL:: [http://arxiv.org/abs/2011.13456](http://arxiv.org/abs/2011.13456)  
  
PDF:: [Song et al. - 2021 - Score-Based Generative Modeling through Stochastic.pdf](zotero://open-pdf/library/items/965N3H9G)  
```