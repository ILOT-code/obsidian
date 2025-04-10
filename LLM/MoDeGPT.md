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
f_{\mathrm{Type-l}}(\boldsymbol{X};\boldsymbol{W}_U,\boldsymbol{W}_D)=\sigma_s(\boldsymbol{X}\boldsymbol{W}_U)\boldsymbol{W}_D,\quad \\f_{\mathrm{Type-ll}}(\boldsymbol{X};\boldsymbol{W}_K^i,\boldsymbol{W}_Q^i)=\sigma_r(\boldsymbol{X}\boldsymbol{W}_{Q,i})\sigma_r^\top(\boldsymbol{X}\boldsymbol{W}_{K,i}),\\f_{\mathrm{Type-III}}(\boldsymbol{X};\boldsymbol{W}_V^i,\boldsymbol{W}_O^i)=\boldsymbol{X}\boldsymbol{W}_{V,i}\boldsymbol{W}_{O,i},
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
$$
\begin{aligned}

\begin{aligned}&\min_{\boldsymbol{S}_k,\boldsymbol{W}_D}\sum_{i=1}^N\|f(\boldsymbol{X}_i)-\sigma(\boldsymbol{X}_i\boldsymbol{W}_U\boldsymbol{S}_k)\hat{\boldsymbol{W}}_D\|_F^2\\&{=}\min_{\boldsymbol{S}_k,\boldsymbol{W}_D}\sum_{i=1}^N\|\sigma(\boldsymbol{X}_i\boldsymbol{W}_U)\boldsymbol{W}_D-\sigma(\boldsymbol{X}_i\boldsymbol{W}_U)\boldsymbol{S}_k\hat{\boldsymbol{W}}_D\|_F^2\\&=\min_{\boldsymbol{S}_k,\boldsymbol{W}_D}\text{ Tr}\left(\sum_{i=1}^N\sigma(\boldsymbol{X}_i\boldsymbol{W}_U)^\top\sigma(\boldsymbol{X}_i\boldsymbol{W}_U)\left(\boldsymbol{W}_D-\boldsymbol{S}_k\hat{\boldsymbol{W}}_D\right)\left(\boldsymbol{W}_D-\boldsymbol{S}_k\hat{\boldsymbol{W}}_D\right)^\top\right)\\&=\min_{\boldsymbol{S}_k,\boldsymbol{W}_D}\|\boldsymbol{C}_\sigma^{\frac12}\left(\boldsymbol{W}_D-\boldsymbol{S}_k\hat{\boldsymbol{W}}_D\right)\|_F^2,\end{aligned}
\end{aligned}

$$
其中， $\boldsymbol{C}_\sigma=\sum_{i=1}^N\sigma(\boldsymbol{X}_i\boldsymbol{W}_U)^\top\sigma(\boldsymbol{X}_i\boldsymbol{W}_U)$。该函数对 $\hat{\mathbf{W}}_{D}$ 求偏导，使其等于 0, 得到 $\hat{\boldsymbol{W}}_D^*=\left(\boldsymbol{S}_k^\top\boldsymbol{C}_\sigma\boldsymbol{S}_k\right)^\dagger\boldsymbol{S}_k^\top\boldsymbol{C}_\sigma\boldsymbol{W}_D$。此结果反代回上面的式子：

$$
\begin{aligned}
\begin{aligned}&\min_{\boldsymbol{S}_k}\left\|\left(\boldsymbol{C}_\sigma^{\frac12}-\boldsymbol{C}_\sigma^{\frac12}\boldsymbol{S}_k\left(\boldsymbol{S}_k^\top\boldsymbol{C}_\sigma\boldsymbol{S}_k\right)^\dagger\boldsymbol{S}_k^\top\boldsymbol{C}_\sigma\right)\boldsymbol{W}_D\right\|_F^2\\&\leq\left\|\boldsymbol{W}_D\right\|_2^2\left\|\boldsymbol{C}_\sigma^{-\frac12}\right\|_2^2\min_{\boldsymbol{S}_k}\left\|\boldsymbol{C}_\sigma-\boldsymbol{C}_\sigma\boldsymbol{S}_k\left(\boldsymbol{S}_k^\top\boldsymbol{C}_\sigma\boldsymbol{S}_k\right)^\dagger\boldsymbol{S}_k^\top\boldsymbol{C}_\sigma\right\|_F^2=\left\|\boldsymbol{W}_D\right\|_2^2\left\|\boldsymbol{C}_\sigma^{-1}\right\|_2\mathcal{E}_{\mathrm{Nys}}^2(\boldsymbol{C}_\sigma).\end{aligned}
\end{aligned}
$$
> $||AB||_{F}\leq ||A||_{F}||B||_{F}$
> 利用柯西-施瓦茨不等式可以证明

那么，这个误差和矩阵 $\mathbf{C}_{\sigma}$ 的 Nyström approximation 分解的误差相关的，后者越小，前者误差也会小。因此，通过对 $\mathbf{C}_{\sigma}$ 进行这个分解，就能得到 $\mathbf{S}_{k}$, 也就能得到 $\hat{\mathbf{W}}_{U},\hat{\mathbf{W}}_{D}$, 误差上界也是有保证的。

## key-query
$$
f_{\mathrm{Type-ll}}(\boldsymbol{X};\boldsymbol{W}_K^i,\boldsymbol{W}_Q^i)=\sigma_r(\boldsymbol{X}\boldsymbol{W}_{Q,i})\sigma_r^\top(\boldsymbol{X}\boldsymbol{W}_{K,i}),\
$$
$\hat{\mathbf{W}}_{K},\hat{\mathbf{W}}_{Q}$ 分别被限定为 $\mathbf{W}_{K}\mathbf{S}_{k}, \mathbf{W}_{Q}\mathbf{S}_{k}$, 他们使用同一个列选择矩阵。


$$
\begin{aligned}&\sum_{i=1}^{N}\|f(\boldsymbol{X}_{i})-\sigma_{r}(\boldsymbol{X}_{i}\boldsymbol{W}_{Q}\boldsymbol{S}_{k})\sigma_{r}^{\top}(\boldsymbol{X}_{i}\boldsymbol{W}_{K}\boldsymbol{S}_{k})\|_{F}^{2}\\&{\operatorname*{\operatorname*{=}}}\sum_{i=1}^{N}\|\sigma_{r}(\boldsymbol{X}_{i}\boldsymbol{W}_{Q})\left(\boldsymbol{I}-\boldsymbol{S}_{k}\boldsymbol{S}_{k}^{\top}\right)\sigma_{r}^{\top}(\boldsymbol{X}_{i}\boldsymbol{W}_{K})\|_{F}^{2}\\&{\operatorname*{\operatorname*{=}}}\sum_{i=1}^{N}\operatorname{Tr}\left(\left(\boldsymbol{I}-\boldsymbol{S}_{k}\boldsymbol{S}_{k}^{\top}\right)\sigma_{r}(\boldsymbol{X}_{i}\boldsymbol{W}_{Q})^{\top}\sigma_{r}(\boldsymbol{X}_{i}\boldsymbol{W}_{Q})\left(\boldsymbol{I}-\boldsymbol{S}_{k}\boldsymbol{S}_{k}^{\top}\right)\sigma_{r}(\boldsymbol{X}_{i}\boldsymbol{W}_{K})^{\top}\sigma_{r}(\boldsymbol{X}_{i}\boldsymbol{W}_{K})\right)\\&\overset{(b)}{\operatorname*{\operatorname*{\leq}}}\operatorname{Tr}\left(\sum_{i=1}^{N}\left(\boldsymbol{I}-\boldsymbol{S}_{k}\boldsymbol{S}_{k}^{\top}\right)\sigma_{r}(\boldsymbol{X}_{i}\boldsymbol{W}_{Q})^{\top}\sigma_{r}(\boldsymbol{X}_{i}\boldsymbol{W}_{Q})\left(\boldsymbol{I}-\boldsymbol{S}_{k}\boldsymbol{S}_{k}^{\top}\right)\sum_{j=1}^{N}\sigma_{r}(\boldsymbol{X}_{j}\boldsymbol{W}_{K})^{\top}\sigma_{r}(\boldsymbol{X}_{j}\boldsymbol{W}_{K})\right)\\&\overset{(c)}{\operatorname*{\operatorname*{\leq}}}\operatorname{Tr}\left(\boldsymbol{C}_{K}\left(\boldsymbol{I}-\boldsymbol{S}_{k}\boldsymbol{S}_{k}^{\top}\right)\boldsymbol{C}_{Q}\right)=\|\boldsymbol{C}_{K}^{{\frac{1}{2}}}\boldsymbol{C}_{Q}^{{\frac{1}{2}}}-\boldsymbol{C}_{K}^{{\frac{1}{2}}}\boldsymbol{S}_{k}\boldsymbol{S}_{k}^{\top}\boldsymbol{C}_{Q}^{{\frac{1}{2}}}\|_{F}^{2}=\varepsilon_{{\mathrm{CR}}}^{2}(\boldsymbol{C}_{K}^{{\frac{1}{2}}}\boldsymbol{C}_{Q}^{{\frac{1}{2}}}),&\end{aligned}
$$

$(b)$ 的原因：