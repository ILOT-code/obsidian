---
title: "MODEGPT: MODULAR DECOMPOSITION FOR  LARGE LANGUAGE MODEL COMPRESSION"
conference: ICLR 2025
---
## Abstract
本文利用矩阵分解技术来压缩大模型的权重。
其特点在于，他对一对权重进行联合的分解，也并不需要额外的参数来进行复原。在推理阶段也不需要额外的计算，同时保留了 LLM 大部分性能。

![[Pasted image 20250410151800.png]]
上图展示了两种“极端”的策略，MoDeGPT 看上去像是两种策略的折衷。
此图展示了 decoder layer 的一个流程，也就是 MHA+MLP 的形式。
**SVD**：svdLLM 方法单独来考虑每一个权重矩阵，单独把他们分解成一对低秩小矩阵的形式。这种做法在推理阶段无需额外的计算，但他也没有节省到计算量。另外，其性能损失也比较大。
**SliceGPT**：他联合优化全部的 6 个矩阵，通过低秩的 $Q$ 矩阵来实现矩阵的压缩，在推理阶段，会额外乘以这个 $Q$ 来进行降维。这种做法性能损失较少，但推理过程引入额外的计算量。
**MoDeGPT**：本文根据计算的顺序把这 6 个矩阵分成了三组，为每一组中的矩阵进行联合优化。并从理论上，为每一组矩阵设计了合适的压缩策略，并分析了误差上界。最终实现了优秀的性能，并且也不会引入额外的计算量。



## 分组
![[Pasted image 20250410153223.png]]
分为了三组：
$$
\begin{aligned}
f_{\mathrm{Type-l}}(\boldsymbol{X};\boldsymbol{W}_U,\boldsymbol{W}_D)=\sigma_s(\boldsymbol{X}\boldsymbol{W}_U)\boldsymbol{W}_D,\quad \\f_{\mathrm{Type-ll}}(\boldsymbol{X};\boldsymbol{W}_K^i,\boldsymbol{W}_Q^i)=\sigma_r(\boldsymbol{X}\boldsymbol{W}_{Q,i})\sigma_r^\top(\boldsymbol{X}\boldsymbol{W}_{K,i}),\\f_{\mathrm{Type-II}}(\boldsymbol{X};\boldsymbol{W}_V^i,\boldsymbol{W}_O^i)=\boldsymbol{X}\boldsymbol{W}_{V,i}\boldsymbol{W}_{O,i},
\end{aligned}

$$

对于每一组而言，需要压缩这两个矩阵，使得对于校准集上的输入能够有最小的差异：
$$
V^*\triangleq\min_{\hat{\boldsymbol{W}}_1,\hat{\boldsymbol{W}}_2}\sum_{i=1}^N\|f(\boldsymbol{X}_i;\boldsymbol{W}_1,\boldsymbol{W}_2)-f(\boldsymbol{X}_i;\hat{\boldsymbol{W}}_1,\hat{\boldsymbol{W}}_2)\|_F^2\text{ such that }(\hat{\boldsymbol{W}}_1,\hat{\boldsymbol{W}}_2)\in\mathcal{C}
$$
$\hat{\boldsymbol{W}}_{1}, \hat{\boldsymbol{W}}_{2}$ 的搜索空间被限定了，来产生满足某种结构要求的矩阵，并降低搜索难度。
对那些和非线性函数紧密联系的矩阵，$\mathbf{W}_{U},\mathbf{W}_{Q},\mathbf{W}_{K}$，他们的压缩矩阵被限定在 $\hat{\mathbf{W}}=\mathbf{W}\mathbf{S}_{k}$，其中 $\mathbf{S}_{k}$ 是一个列选择矩阵。这样做的主要原因是可以获得一种交换性：$\sigma(WS)=\sigma(W)S$，对之后的理论推导有很多好处。而对其他的矩阵，不做任何限制 (但需要符合矩阵乘的要求)。

有三种有名的矩阵分解技术，他们都关注于把一个矩阵分解成两个矩阵乘的形式，但目标不同。比如说 SVD 的目标是使得范数差最小。
![[Pasted image 20250410154813.png]]
作者发现，求解这三组优化问题和这三种矩阵分解技术密切相关。
![[Pasted image 20250410155410.png]]

## MLP layer
$$
f_{\mathrm{Type-l}}(\boldsymbol{X};\boldsymbol{W}_U,\boldsymbol{W}_D)=\sigma_s(\boldsymbol{X}\boldsymbol{W}_U)\boldsymbol{W}_D
$$
$\hat{\mathbf{W}}_{U}$ 被限定在 $\mathbf{W}_{U}\mathbf{S}_{k}$ 中，$\hat{\mathbf{W}}_{D}$ 的空间是 $\mathbb{R}^{k\times d}$。
