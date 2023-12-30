---
zotero-key: KMABZSUJ
zt-attachments:
  - "271"
title: Towards Efficient Image Compression Without Autoregressive Models
authors:
  - Muhammad Salman Ali
  - Yeongwoong Kim
  - Maryam Qamar
  - Sung-Chang Lim
  - Donghyun Kim
  - Chaoning Zhang
  - Sung-Ho Bae
  - Hui Yong Kim
conference: NIPS2023
citekey: ali2023-efficient
tags:
---
# Towards Efficient Image Compression Without Autoregressive Models

**文章链接**: [Zotero](zotero://select/library/items/KMABZSUJ) 
**网页链接**: [URL](https://openreview.net/forum?id=1ihGy9vAIg)
## Abstract

>[!abstract]
>Recently, learned image compression (LIC) has garnered increasing interest with its rapidly improving performance surpassing conventional codecs. A key ingredient of LIC is a hyperprior-based entropy model, where the underlying joint probability of the latent image features is modeled as a product of Gaussian distributions from each latent element. Since latents from the actual images are not spatially independent, autoregressive (AR) context based entropy models were proposed to handle the discrepancy between the assumed distribution and the actual distribution. Though the AR-based models have proven effective, the computational complexity is significantly increased due to the inherent sequential nature of the algorithm. In this paper, we present a novel alternative to the AR-based approach that can provide a significantly better trade-off between performance and complexity. To minimize the discrepancy, we introduce a correlation loss that forces the latents to be spatially decorrelated and better fitted to the independent probability model. Our correlation loss is proved to act as a general plug-in for the hyperprior (HP) based learned image compression methods. The performance gain from our correlation loss is ‘free’ in terms of computation complexity for both inference time and decoding time. To our knowledge, our method gives the best trade-off between the complexity and performance: combined with the Checkerboard-CM, it attains **90%** and when combined with ChARM-CM, it attains **98%** of the AR-based BD-Rate gains yet is around **50 times** and **30 times** faster than AR-based methods respectively

## Comments



## 背景
 Since latents from the actual images are not spatially independent, autoregressive (AR) context based entropy models were proposed to handle the discrepancy between the assumed distribution and the actual distribution. Though the AR-based models have proven effective, the computational complexity is significantly increased due to the inherent sequential nature of the algorithm.

latent 空间的实际上并不上相互独立的，因此端到端图像压缩提出了基于自回归的上下文的熵模型来处理假设分布和实际分布之间的差异。这虽然有效，但极大增加了计算量。
本文提出了一种方法，能够减少 latent 的假设和实际分布的差异。

## 在损失函数中添加协方差损失
$$
Corr\_Map_{k\times k}[i]=\mathbb{E}_{x\sim p(x)}\left[\left(\frac{y_i-\mu_i}{\sigma_i}\right)\left(\frac{y_m-\mu_m}{\sigma_m}\right)\right],0\leq i<k^2
$$
$k\cdot k$ 是窗口的大小，$m$ 是窗口的中心，$\sigma$ 是：
$$
p_{\hat{y}|\hat{z}}\left(\hat{y}_i\mid\hat{z}\right)=\left[\mathcal{N}\left(\mu_i,\sigma_i^2\right)*\mathcal{U}\left(-\frac12,\frac12\right)\right](\hat{y}_i)
$$
对于点的方差。
![[Pasted image 20231126171159.png]]
将这个窗口在整个 latent 上滑动，每个位置取所有窗口对应位置的均值，最后和一个 mask 相乘。
$$
Masked\_Map_{k\times k}[i]=Corr\_Map_{k\times k}[i]\odot Mask.
$$
得到最后的损失函数：
$$
L_{corr}=||Masked\_Map_{k\times k}[i]||^2.
$$
$$
RD_{loss}=E_{x\sim p_{(x)}}\left[-\log_2p_{\hat{y}|\hat{z}}(\hat{y}\mid\hat{z})-\log_2p_{\hat{z}}(\hat{z})\right]+\lambda\cdot E_{x\sim p_{(x)}}[d(x,\hat{x})]+\alpha\cdot[L_{corr}],
$$
