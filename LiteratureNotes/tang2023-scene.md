---
zotero-key: RFVNJ6JH
zt-attachments:
  - "306"
title: "Scene Matters: Model-based Deep Video Compression"
authors:
  - Lv Tang
  - Xinfeng Zhang
  - Gai Zhang
  - Xiaoqi Ma
conference: ICCV2023
citekey: tang2023-scene
tags:
---
# Scene Matters: Model-based Deep Video Compression

**文章链接**: [Zotero](zotero://select/library/items/RFVNJ6JH) 
**网页链接**: [URL](https://openaccess.thecvf.com/content/ICCV2023/html/Tang_Scene_Matters_Model-based_Deep_Video_Compression_ICCV_2023_paper.html)
## Abstract

>[!abstract]
>>These methods typically rely on <mark style="background: #FFF3A3A6;">signal prediction</mark> theory to enhance compression performance by designing high efficient intra and inter prediction strategies and compressing video frames one by one.
>
>传统视频压缩以及之前的基于深度学习的方法通常是依赖于信号预测技术（传统方法就是分块技术，还有帧间帧内的预测；深度学习的模型也是使用神经网络来设计预测模块），以此来减少冗余信息。
>
>>Our proposed MVC directly models the intensity variation of the entire video sequence in one scene, seeking non-redundant representations instead of reducing redundancy through spatio-temporal predictions
>
>本文提出了一种新的框架，将 scenes 视为视频的基本单元，直接对视频序列中 intensity 的变化进行建模，以此来寻找无冗余的压缩，而不是采用预测技术来减少冗余。
>为了达到这个效果，本文提出了一些技巧来增强序列建模的能力：
>**对空间上下文的加强**：<mark style="background: #FF5582A6;">context-related spatial positional embedding</mark>，<mark style="background: #FF5582A6;">frequency domain supervision</mark>
>**对时间上下文的加强**：<mark style="background: #FF5582A6;">scene flow constrain mechanism </mark>，<mark style="background: #FF5582A6;">temporal contrastive loss</mark>


## Introduction

在之前的方法采用的预测技术中，渐进地一块一块或一帧一帧来压缩，预测通常只能依赖于临近的像素点或帧，但视频通常具有较高的 fps，相同的场景往往能在相隔较远的帧出现，因此现有的块级别或帧级别的预测技术很难去处理好场景的冗余。下面的图能够说明这个问题：
![[Pasted image 20231205134831.png]]
于是，本文希望为视频中相同的场景找到紧凑的表达，使用对场景的隐式的紧凑子空间建模，来取代预测。本文采用 INR 来作为骨干网络。

在视频压缩中，序列建模式主要的挑战，但现有的 INR 网络这项能力是有限的，如果将它们直接应用在视频压缩的话，其性能是有限的，在有限的带宽下，它们的重建能力并不好。
![[Pasted image 20231205140537.png]]
**空间上下文**：
问题：现有的 INR 模型需要一个可学习的网络来捕获空间上下文，一些文章也提出了频率感知的操作去提高上下文捕获能力和对高频信号的捕获能力；这些操作都需要额外的参数，降低了整个模型的压缩能力。
方案：
- 上下文捕获：一种新的嵌入手段（propose a context-related spatial positional embedding (CRSPE) method in this paper）
- 频域感知：增加了频域的损失函数去限制（introduce a frequency domain supervision (FDS) module that can capture high-frequency details without requiring additional bitrates.）
**时间上下文**：
问题：现有的 INR 模型主要依靠不同的时间坐标编码手段来处理时间上下文信息，期望模型能够学习到这些信息，但它们很难探索到复杂的时间信息，尤其是对于长时间序列而言。
方案：
- 短时：引入了有关光流的损失函数（introduce a scene flow constraint mechanism (SFCM) for short-term temporal correlation）
- 长时：temporal contrastive loss (TCL) for long-term temporal correlation


## Method

![[Pasted image 20231205143745.png]]

序列数据的编码：
$$
\begin{aligned}
\gamma(t)&=(\sin\left(b^0\pi t\right),\cos\left(b^0\pi t\right),\ldots,\sin\left(b^{l-1}\pi t\right),\cos\left(b^{l-1}\pi t\right))
\end{aligned}
$$
模块使用的网络如下：
MLP: $\mathcal G_{\theta_{t1}}$ $\mathcal G_{\theta_{t2}}$ $\mathcal G_{\theta_{f}}$ 
Transformer: $\mathcal G_{\theta_{s}}$ 
CNN: $\phi$ $\psi$

首先肯定有一个重建损失：
$$\mathcal{L}_{spa}=\mathcal{L}_1(\mathbf{\hat{v}}_t,\mathbf{v}_t)$$
### Context-related Spatial Positional Embedding
现有的基于 INR 的方法通过固定的网格坐标 $S$ 来隐式地表达空间上下文。作者认为不同帧之间内容可能很不相同，仅仅使用固定的 $S$ 并不合适，因此将原本的 $S$ 和一个和当前帧信息有关的 $F_e$ 合并。
$F_e$ 和由当前帧进过卷积得到，作者将 $F_e$ 的形状设置为 $S$ 一样。
![[Pasted image 20231205154452.png]]
### Frequency Domain Supervision
将原图和重建的图片分别使用 FFT，对它们计算得到频率项损失函数。
$$
\mathcal{L}_{freq}=\mathcal{L}_1(\mathbf{FFT}(\mathbf{\hat{v}}_t),\mathbf{FFT}(\mathbf{v}_t))
$$

### Scene Flow Constrain Mechanism
$\mathcal G_{\theta_{r}}$ 帧重建网络由 5 个上采样组成，在最后一个上采样阶段，设输入是 $\hat{\mathbf{Z}}_t$,，输出是重建的帧 $\hat v_t$。
为了捕获短时的时间上下文，作者使用 $t$ 与 $t-1$ 时刻的光流来构造一项损失来实现。经过重建的帧之间的光流如果与 GT 越相近越好。
对于 GT，由于没有标签，作者使用了 RAFT 算法（目前的 SOTA）来估计 $V_{t},V_{t-1}$ 之间的光流，把它当作 GT。

本文使用了一个预测模型 $\phi$ 来作用于 $\hat Z$ ，来产生重建的光流。

$\textbf{O}_t^f$ 是 $t$ 到 $t-1$ 的光流，$\mathbf{O}_t^b$ 是 $t-1$ 到 $t$ 的光流。
$$\mathcal{L}_{vanilla-flow}=\mathcal{L}_1(\mathbf{O}_t^f,\mathbf{O}_t^{fgt})+\mathcal{L}_1(\mathbf{O}_t^b,\mathbf{O}_t^{bgt})$$


然而，由于 GT 是由 RAFT 计算的，而不是真实值，存在噪声，需要被正规化。作者采用了一个 MASK 矩阵的方法：
对 $\hat Z_t$ 使用 $1 \times 1$ 的卷积，并经过 softmax 操作，产生两个通道的正规化矩阵 $W$，使用第二个通道，$W^{(1)}$ 去重写上面的损失函数，得到：
$$\begin{aligned}
\mathcal{L}_{flow}= \mathcal{L}_1(\mathbf{W}^{(1)}\cdot\mathbf{O}_t^f,\mathbf{W}^{(1)}\cdot\mathbf{O}_{t}^{fgt})+\mathcal{L}_1(\mathbf{W}^{(1)}\cdot\mathbf{O}_t^b,\mathbf{W}^{(1)}\cdot\mathbf{O}_t^{bgt})
\end{aligned}$$
另外，应该让 $W$ 跟倾向于不均匀的分布，否则加不加这个 $W$ 没什么区别。设法让它关注某些光流子集的差异，为此，作者引入下面的损失函数：
$$\mathcal{L}_{ent}=-(\mathbf{W}^{(0)}\cdot log(\mathbf{W}^{(0)})+\mathbf{W}^{(1)}\cdot log(\mathbf{W}^{(1)}))$$
作者为有正规化和无正规化作了对比：
![[Pasted image 20231205190548.png]]
### Temporal Contrastive Loss
当前帧和之前的 $L$ 帧之间都可能具有相似性，并且，相隔越远的帧之间的相似性应该越低。因此，作者提出了一个假设，帧之间的相似性和帧之间的距离是有一个高斯分布的关系：
$$
Gau(x)~=\frac1{\sigma\sqrt{2\pi}}\exp\left(-\frac{x^2}{2\sigma^2}\right)
$$

作者认为模型得到的当前重建帧 $\hat V_t$ 和前 $L$ 重建帧序列 $\large \hat V_t^p$ 之间的相关性，应该靠近前面的高斯分布，因此作者提出了基于 KL 熵的损失函数，将帧之间的相关性归一化成为概率后，和高斯分求相对熵。
![[Pasted image 20231205190653.png]]

对于帧之间的相关性的计算，作者受 SimCLR 文提出的 contrastive learning 启发，认为越相似的两个重建帧，进过编码（特征提取）后，得到的向量越接近就意味着帧之间越接近。本文的特征提取网络采用了经过预训练的 ResNet,作者采用余弦相似度作为衡量标准：
$$\sin(\boldsymbol{u},\boldsymbol{v})=\frac{\boldsymbol{u}^{T}\boldsymbol{v}}{\|\boldsymbol{u}\|\|\boldsymbol{v}\|}$$
基于此：设 $\hat V_t$ 和 $\large \hat V_t^p$ 进过编码后，得到的向量 $\mathbf h_t$ $\large \mathbf H_t^p$.

$$\begin{aligned}
\mathcal{L}_{cont}&=
-\sum_{j=t-L}^{t-1}w_{tj}\log\frac{\exp\left(\sin\left(\mathbf{h}_t,\mathbf{h}_j\right)/\tau\right)}{\sum_{k=t-L}^{t-1}\exp\left(\sin\left(\mathbf{h}_t,\mathbf{h}_k\right)/\tau\right)}+const,  \\
w_{tj}& =\frac{Gau\left(t-j\right)}{\sum_{k=t-L}^{t-1}Gau\left(t-k\right)}. 
\end{aligned}$$
## Experiments
本文使用 pytorch 模块来构建模型，使用 PSNR 作为评估指标，使用了会议视频数据（来自 HEVC ClassE，720p）和监控视频 (来自 IEEE 1857 1080p)。


和几种 MVC 方法的对比 (以 H.266 为对比基准)：
![[Pasted image 20231205190833.png]]

解码时间对比：
![[Pasted image 20231205191100.png]]

消融实验：
Architecure 展示了渐进地增加模块对性能的影响
SFCM 展示了正则化操作与 $\mathcal{L}_{ent}$ 的影响，它们分别于 +CRSPE+FDS 与 +CRSPE+FDS+SFCM 对比
TCL 展示了参数 $L$ 的影响以及高斯先验的影响
![[Pasted image 20231205192421.png]]