---
zotero-key: 2ES2TF82
zt-attachments:
  - "356"
title: Video Compression With Entropy-Constrained Neural Representations
authors:
  - Carlos Gomes
  - Roberto Azevedo
  - Christopher Schroers
conference: CVPR2023
citekey: gomes2023-video
tags:
---
# Video Compression With Entropy-Constrained Neural Representations

**文章链接**: [Zotero](zotero://select/library/items/2ES2TF82) [attachment](<file:///home/ilot/Zotero/storage/SUVWIVMH/Gomes%20%E7%AD%89%20-%202023%20-%20Video%20Compression%20With%20Entropy-Constrained%20Neural%20.pdf>)
**网页链接**: [URL](https://openaccess.thecvf.com/content/CVPR2023/html/Gomes_Video_Compression_With_Entropy-Constrained_Neural_Representations_CVPR_2023_paper.html)
## Abstract

>[!abstract]
>neural video representation 和传统的一些视频压缩技术仍然存在 gap，这有两方面的因素：
>1. 此类方法的网络结构很简单，无法获得紧凑的表达形式，参数的使用很没有效率
>2. 失真控制和码率控制没有同步进行
>为此，提出了一种新颖的卷积结构的视频表示方法，能够联合优化率-失真。


## Architecture
把当前帧 $t \in[0,1)$ 扩展到 $T\in \mathbb{R}^{1\times h\times w}$，并把它和一个固定的坐标网格 $M\in \mathbb{R}^{2\times h\times w},whereM[0,h^{\prime},w^{\prime}]=\frac{w^{\prime}}{W},M[1,h^{\prime},w^{\prime}]=\frac{h^{\prime}}{W}$ 结合。再 element-wise 使用位置编码：

$$
\gamma(x)=(\sin(1.25^0\pi x),\cos(1.25^0\pi x),\dots,\sin(1.25^{L-1}\pi x,\cos(1.25^{L-1}\pi x))
$$
采用了和 E-NERV 类似的结构。卷积上采样共有 5 个块，AdaIN 模块为它们各学习一个 $\mu ，\sigma$，时间信息经 AdaIN 处理后输入这些层。
![[Pasted image 20240906111014.png]]

## Entropy Minimization
权重参数需要被量化，$\hat{\theta}=Q_{\gamma}(\theta)$，被解码后，再反量化回来 $\theta ^\prime=Q_{\gamma}^{-1}(\hat{\theta})$. 整体的损失函数即为：
$$
\min_{\theta}\sum_{(x,y)\in S}D(f(x;Q_{\gamma}^{-1}(\hat{\theta})),y)+\lambda \sum_{i=0}^{|\hat{\theta}|}-\log_{2}\hat{p}(\hat{\theta_{i}})
$$
使用的量化操作是：
$$
\begin{aligned}
Q_{\gamma}:\mathbb{R}\to \mathbb{Z},Q_{\gamma}(x)=\lfloor \frac{x+\beta}{\alpha} \rceil , \gamma=(\alpha,\beta)\\
Q_{\gamma}^{-1}=x\times \alpha-\beta
\end{aligned} 
$$

克服不可微的方法有两种：
1. STE
2. 加噪声，$\tilde{\theta}=\frac{x+\beta}{\alpha}+u$

实际上作者同时使用了这两种方法。在计算损失函数的第一项时，对参数首先使用了 STE 进行处理，计算失真。在计算第二项时，是对参数进行加噪声的处理，再使用熵模型来计算 rate。

作者把每一层内部的参数，建模成独立同分布模型。（作者声称采用过联合概率建模的方法，但效果不好）。并使用 [[balle2018-variational|(Johannes Ballé, 2018)]] 中那个对 $z$ 进行概率建模的模型。

最终损失函数可以写为：
$$
\min _{\theta,\phi,\boldsymbol{\gamma}}\sum_{(x,y)\in S}D(f(x;Q^{-1}(Q_{ste}(\theta;\boldsymbol{\gamma});\boldsymbol{\gamma})),y)+\lambda\frac{\sum_{l\in layers}-\log_{2}p_{\phi}(Q_{noise}(\theta _{l};\gamma_{l}))}{frames\times h\times w}
$$
rate 项除以了总像素数，这样 $\lambda$ 就和视频的分辨率和帧数无关了。


## Experiments

### 容量测试
为验证这个架构的容量而作的实验，因此不使用熵模型和量化。
![[Pasted image 20240906121016.png|500]]
### 视频压缩
![[Pasted image 20240906121336.png]]

### 微调
把熵优化的过程看成微调。前 $0.8epoch$ 过程中，$\lambda=0$，后 $0.2epoch$ 才使用真正的 $\lambda$。
实验发现这种策略加速了训练过程，性能损失很小。


## Comments

### 先前研究的问题

> [!note] Page 1
> 
> This performance gap can be explained by the fact that current NVR methods: i) use architectures that do not efficiently obtain a compact representation of temporal and spatial information; and ii) minimize rate and distortion disjointly (first overfitting a network on a video and then using heuristic techniques such as post-training quantization or weight pruning to compress the model).
> 
> ---
> 🔤这种性能差距可以通过以下事实来解释：当前的 NVR 方法： i) 使用的架构不能有效地获得时间和空间信息的紧凑表示； ii) 不相交地最小化速率和失真（首先在视频上过度拟合网络，然后使用启发式技术（例如训练后量化或权重修剪）来压缩模型）。🔤
> ^NH5XTGX4aSUVWIVMHp1

> [!note] Page 2
> 
> Previous efforts in using INRs for video compression, however, have mostly treated the problems of representing the input signal with high fidelity and compressing it as mostly disjoint tasks. A network is first trained to minimize a distortion loss and is then put through some procedure to reduce its size, e.g., storing its weights in a 16-bit floating point format, quantization, or pruning [7, 10, 29]. Furthermore, current architectures are not designed with parameter efficiency as a priority, suffering from an inefficient allocation of parameters, which can be prohibitive for the task of video compression [7, 16].
> 
> ---
> 🔤然而，之前使用 INR 进行视频压缩的努力大多将高保真度表示输入信号并将其压缩为大多数不相交任务的问题来处理。首先训练网络以最小化失真损失，然后通过一些程序来减小其大小，例如，以 16 位浮点格式存储其权重、量化或修剪 [7,10,29]。此外，当前的架构在设计时并未将参数效率作为优先考虑因素，导致参数分配效率低下，这可能无法完成视频压缩任务[7, 16]。🔤
> ^H73CDBESaSUVWIVMHp2

### 创新点

> [!note] Page 1
> 
> We propose a novel convolutional architecture for video representation that better represents spatio-temporal information and a training strategy capable of jointly optimizing rate and distortion.
> 
> ---
> 🔤我们提出了一种新颖的视频表示卷积架构，可以更好地表示时空信息，以及能够联合优化速率和失真的训练策略。🔤
> ^S7HQ88SPaSUVWIVMHp1

> [!note] Page 2
> 
> In addition, drawing on information theory, we propose a theoretically motivated R-D formulation for video compression with INRs that jointly minimizes rate and distortion.
> 
> ---
> 🔤此外，利用信息论，我们提出了一种基于理论的 R-D 视频压缩公式，其 INR 可以共同最小化速率和失真。🔤
> ^8B7RS4QMaSUVWIVMHp2

> [!note] Page 3
> 
> a more efficient neural network architecture for video compression (Section 3.1) and the formalization of the task as an R-D problem (Section 3.2).
> 
> ---
> 🔤用于视频压缩的更高效的神经网络架构（第 3.1 节）以及将任务形式化为 R-D 问题（第 3.2 节）。🔤
> ^DTR3I2EZaSUVWIVMHp3

### 性能成绩

> [!note] Page 1
> 
> Moreover, we deliver the first NVR-based video compression method that improves over the typically adopted HEVC benchmark (x265, disabled b-frames, “medium” preset), closing the gap to autoencoder-based video compression techniques.
> 
> ---
> 🔤此外，我们提供了第一个基于 NVR 的视频压缩方法，该方法比通常采用的 HEVC 基准（x265、禁用 B 帧、“中”预设）有所改进，缩小了与基于自动编码器的视频压缩技术的差距。🔤
> ^N72RRVAUaSUVWIVMHp1

### 技术细节

> [!note] Page 2
> 
> We build on the work of Oktay et al.
> 
> ---
> 🔤我们以 Oktay 等人的工作为基础。🔤
> ^VXP7LCAVaSUVWIVMHp2

> [!note] Page 5
> 
> To fit this paradigm, we define: i) a quantization function Qγ, with learnable parameters γ, mapping continuous weights to discrete symbols, and ii) a dequantization function Q−1 γ , mapping the symbols to the values at the center of their respective quantization bins.
> 
> ---
> 🔤为了适应这种范式，我们定义：i）量化函数 Qγ，具有可学习参数 γ，将连续权重映射到离散符号，以及 ii）反量化函数 Q−1 γ，将符号映射到其各自中心的值量化仓。🔤
> ^VTSFHHMSaSUVWIVMHp5

> [!note] Page 5
> 
> where ˆ p is the pmf of ˆ θ, which can be readily computed. To optimize this loss, the process minimizes the distortion by learning parameters θ that can appropriately represent the signal, and γ that provide a small enough quantization error. Simultaneously, the distribution of Qγ(θ) must also have a sufficiently small entropy, to minimize the R.
> 
> ---
> 🔤其中 ˆ p 是 ˆ θ 的 pmf，很容易计算。为了优化这种损失，该过程通过学习可以适当表示信号的参数 θ 和提供足够小的量化误差的 γ 来最小化失真。同时，Qγ(θ) 的分布也必须具有足够小的熵，以最小化 R。🔤
> ^YJ6875QSaSUVWIVMHp5

> [!note] Page 5
> 
> From the above, we identify the two error sources introduced in this process.
> 
> ---
> 🔤综上所述，我们识别出了该过程中引入的两个误差源。🔤
> ^82FWZ3ZRaSUVWIVMHp5

> [!note] Page 5
> 
> We define these as two functions, Qnoise and Qste. As in Balle et al. [4], we obtain the best results using Qste for calculating the distortion metric, as it avoids the introduction of random noise, and Qnoise for calculating the entropy term.
> 
> ---
> 🔤我们将它们定义为两个函数：Qnoise 和 Qste。正如 Balle 等人所言。 [4]，我们使用 Qste 计算失真度量获得了最佳结果，因为它避免了引入随机噪声，并使用 Qnoise 计算熵项。🔤
> ^5Q9EDDHVaSUVWIVMHp5

> [!note] Page 6
> 
> To model the weights of the neural network, we interpret weights in each layer as being generated from an independent source. Within each layer, weights are taken as i.i.d. samples.
> 
> ---
> 🔤为了对神经网络的权重进行建模，我们将每层中的权重解释为从独立源生成。在每一层中，权重被视为独立同分布。样品。🔤
> ^763NFYKNaSUVWIVMHp6

