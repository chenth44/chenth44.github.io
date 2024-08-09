---  
tags:  
  - Paper  
  - Machine-Learning/Deep-Learning/Generative-Model/Diffusion-Model  
aliases:   
citekey: hoClassifierFreeDiffusionGuidance2022  
authors:  
  - Jonathan Ho  
  - Tim Salimans  
date: 2022-07-25  
collections:  
  - Diffusion-Models  
local-library: zotero://select/items/1_H6ACYPZ8  
cloud-library: http://zotero.org/users/10051742/items/H6ACYPZ8  
comment:   
finished-reading: false  
---  
  
  
# Classifier-Free Diffusion Guidance  
  
## Abstract  
  
Classiﬁer guidance is a recently introduced method to trade off mode coverage and sample ﬁdelity in conditional diffusion models post training, in the same spirit as low temperature sampling or truncation in other types of generative models. Classiﬁer guidance combines the score estimate of a diffusion model with the gradient of an image classiﬁer and thereby requires training an image classiﬁer separate from the diffusion model. It also raises the question of whether guidance can be performed without a classiﬁer. We show that guidance can be indeed performed by a pure generative model without such a classiﬁer: in what we call classiﬁer-free guidance, we jointly train a conditional and an unconditional diffusion model, and we combine the resulting conditional and unconditional score estimates to attain a trade-off between sample quality and diversity similar to that obtained using classiﬁer guidance.  
  
  
## Introduction  
  
## Related Work  
  
## Methods  
  
## Results and Discussions  
  
```ad-seealso  
title: Also see  
URL:: [http://arxiv.org/abs/2207.12598](http://arxiv.org/abs/2207.12598)  
PDF:: [Ho and Salimans - 2022 - Classifier-Free Diffusion Guidance.pdf](zotero://open-pdf/library/items/YT5EHSCM)  
```