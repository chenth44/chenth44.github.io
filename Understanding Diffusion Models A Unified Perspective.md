---  
tags:  
  - Paper  
  - Paper/Survey  
  - Machine-Learning/Deep-Learning/Generative-Model/Diffusion-Model  
aliases:  
  - Understanding Diffusion Models  
citekey: luoUnderstandingDiffusionModels2022  
authors:  
  - Calvin Luo  
date: 2022-08-25  
collections:  
  - Diffusion-Models  
local-library: zotero://select/items/1_CWI3N898  
cloud-library: http://zotero.org/users/10051742/items/CWI3N898  
comment:   
finished-reading: true  
---  
  
  
# Understanding Diffusion Models: A Unified Perspective  
  
## Abstract  
  
Diffusion models have shown incredible capabilities as generative models; indeed, they power the current state-of-the-art models on text-conditioned image generation such as Imagen and DALL-E 2. In this work we review, demystify, and unify the understanding of diffusion models across both variational and score-based perspectives. We first derive Variational Diffusion Models (VDM) as a special case of a Markovian Hierarchical Variational Autoencoder, where three key assumptions enable tractable computation and scalable optimization of the ELBO. We then prove that optimizing a VDM boils down to learning a neural network to predict one of three potential objectives: the original source input from any arbitrary noisification of it, the original source noise from any arbitrarily noisified input, or the score function of a noisified input at any arbitrary noise level. We then dive deeper into what it means to learn the score function, and connect the variational perspective of a diffusion model explicitly with the Score-based Generative Modeling perspective through Tweedie's Formula. Lastly, we cover how to learn a conditional distribution using diffusion models via guidance.  
  
  
## Introduction  
  
## Related Work  
  
## Methods  
  
## Results and Discussions  
  
```ad-seealso  
title: Also see  
URL:: [http://arxiv.org/abs/2208.11970](http://arxiv.org/abs/2208.11970)  
  
PDF:: [Luo - 2022 - Understanding Diffusion Models A Unified Perspect.pdf](zotero://open-pdf/library/items/6GP3T8Y8)  
```