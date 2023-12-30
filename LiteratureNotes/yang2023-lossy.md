---
zotero-key: H3BNQWYL
zt-attachments:
  - "262"
title: Lossy Image Compression with Conditional Diffusion Models
authors:
  - Ruihan Yang
  - Stephan Mandt
doi: 10.48550/arXiv.2209.06950
conference: NIPS2023
citekey: yang2023-lossy
tags:
---
# Lossy Image Compression with Conditional Diffusion Models

**文章链接**: [Zotero](zotero://select/library/items/H3BNQWYL) 
**网页链接**: [URL](http://arxiv.org/abs/2209.06950)
## Abstract

>[!abstract]
>This paper outlines an end-to-end optimized lossy image compression framework using diffusion generative models. The approach relies on the transform coding paradigm, where an image is mapped into a latent space for entropy coding and, from there, mapped back to the data space for reconstruction. In contrast to VAE-based neural compression, where the (mean) decoder is a deterministic neural network, our decoder is a conditional diffusion model. Our approach thus introduces an additional "content" latent variable on which the reverse diffusion process is conditioned and uses this variable to store information about the image. The remaining "texture" variables characterizing the diffusion process are synthesized at decoding time. We show that the model's performance can be tuned toward perceptual metrics of interest. Our extensive experiments involving multiple datasets and image quality assessment metrics show that our approach yields stronger reported FID scores than the GAN-based model, while also yielding competitive performance with VAE-based models in several distortion metrics. Furthermore, training the diffusion with X-parameterization enables high-quality reconstructions in only a handful of decoding steps, greatly affecting the model's practicality.

## Comments

## Contrbutions
- We propose a novel transform-coding-based lossy compression scheme using diffusion models.
  使用扩散模型的扩散和去噪过程代替之前的 encoder 和 decoder。对原图片 $x_0$ 编码得到对图片信息的描述 $z$，在去噪过程中，引入 $z$ 来指导重建 $x$。
  [[balle2017-endtoend|(Johannes Ballé, 2017)]] 提到过端到端的压缩模型都能看成 VAE ，而 DM 是多层的 VAE，也许这就是作者的想法来源。

  ![[Pasted image 20231126151612.png|500]]
  - We derive our model’s loss function from a variational upper bound to the diffusion model’s implicit rate-distortion function.
    利用 DM 模型的损失函数来重写之前模型损失函数的"损失项"，并引入了 perceptual metrics。


## 损失函数
$$
\begin{aligned}
&\mathbb{E}_{\mathbf{z}\sim e(\mathbf{z}|\mathbf{x}_0)}[-\log p(\mathbf{x}_0|\mathbf{z})-\lambda\log p(\mathbf{z})] \\
&\leq \mathbb{E}_{\mathbf{z}\sim e(\mathbf{z}|\mathbf{x}_0)}\left[L_{\mathrm{upper}}(\mathbf{x}_0|\mathbf{z})-\lambda\log p(\mathbf{z})\right] \\
&where  \begin{aligned}L_{\text{upper}}( \mathbf{x}_{0}|\mathbf{z})&=-\mathbb{E}_{\mathbf{x}_{1:N}\sim q(\mathbf{x}_{1:N}|\mathbf{x}_{0})}\left[\log\frac{p(\mathbf{x}_{0:N}|\mathbf{z})}{q(\mathbf{x}_{1:N}|\mathbf{x}_{0})}\right]\end{aligned}
\end{aligned}
$$
就是借用之前模型的损失函数，将重建损失替换为 DM 的损失函数。
$$
L_\text{upper}(\mathbf{x}_0|\mathbf{z})\approx\mathbb{E}_{\mathbf{x}_0,n,\epsilon}||\epsilon-\epsilon_\theta(\mathbf{x}_n,\mathbf{z},\frac{n}{N_{\mathrm{train}}})||^2=\mathbb{E}_{\mathbf{x}_0,n,\epsilon}\frac{\alpha_n}{1-\alpha_n}||\mathbf{x}_0-\mathcal{X}_\theta(\mathbf{x}_n,\mathbf{z},\frac{n}{N_{\mathrm{train}}})||^2
$$
$\mathcal{X}$ 被训练预测 $\mathbf{x}_0$。为了自由的决定去噪层次，将 $\mathcal{X}$ 的参数由 $n$ 改为 $\frac{n}{N_{\mathrm{train}}}$.

一般情况下，预测噪声的模型比预测 $\mathbf{x}_0$ 的模型能得到更高的重建精度，作者之所以这里采用的 $\mathcal{X}$，是因为他申称能够大大减少去噪过程的层数，仅仅少量的去噪过程就能得到不错的效果。此预测模型由 [[salimans2022-progressive|(Tim Salimans, 2022)]] 提出。

最后，作者提出引入 perceptual metrics
$$
\begin{gathered}L_{p}=\mathbb{E}_{\epsilon,n,\mathbf{z}\sim e(\mathbf{z}|\mathbf{x}_0)}[d(\bar{\mathbf{x}}_0,\mathbf{x}_0)]\mathrm{~and~}L_{c}=\mathbb{E}_{\mathbf{z}\sim e(\mathbf{z}|\mathbf{x}_0)}[L_{\mathrm{upper}}(\mathbf{x}_0|\mathbf{z})-\frac{\lambda}{1-\rho}\log p(\mathbf{z})]\\L=\rho L_{p}+(1-\rho)L_{c}.\end{gathered}
$$

## 训练与编解码过程
![[Pasted image 20231126160310.png]]

训练过程和条件控制扩散模型一样，$z$ 的量化也是加上均匀噪声
在解码过程中，使用
编码也就是将量化的 $z$ 编码

## 模型结构
![[Pasted image 20231126160954.png]]

其中，超先验模型采用了 Minnen 2018 的结构