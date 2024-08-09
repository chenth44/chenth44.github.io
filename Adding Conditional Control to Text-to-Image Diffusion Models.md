---
tags:
  - Paper
  - Machine-Learning/Deep-Learning/Generative-Model/Diffusion-Model
aliases:
  - ControlNet
citekey: zhangAddingConditionalControl2023
authors:
  - Lvmin Zhang
  - Anyi Rao
  - Maneesh Agrawala
date: 2023-10-1
collections:
  - Diffusion-Models
local-library: zotero://select/items/1_34Q9CDJF
cloud-library: http://zotero.org/users/10051742/items/34Q9CDJF
comment: 
finished-reading: false
---


# Adding Conditional Control to Text-to-Image Diffusion Models

## Abstract

We present ControlNet, a neural network architecture to add spatial conditioning controls to large, pretrained textto-image diffusion models. ControlNet locks the productionready large diffusion models, and reuses their deep and robust encoding layers pretrained with billions of images as a strong backbone to learn a diverse set of conditional controls. The neural architecture is connected with “zero convolutions” (zero-initialized convolution layers) that progressively grow the parameters from zero and ensure that no harmful noise could affect the finetuning. We test various conditioning controls, e.g., edges, depth, segmentation, human pose, etc., with Stable Diffusion, using single or multiple conditions, with or without prompts. We show that the training of ControlNets is robust with small (<50k) and large (>1m) datasets. Extensive results show that ControlNet may facilitate wider applications to control image diffusion models.


## Introduction

## Related Work

## Methods

## Results and Discussions

```ad-seealso
title: Also see
URL:: [https://ieeexplore.ieee.org/document/10377881/](https://ieeexplore.ieee.org/document/10377881/)
PDF:: [Zhang et al. - 2023 - Adding Conditional Control to Text-to-Image Diffus.pdf](zotero://open-pdf/library/items/X7G4V3TJ)
```