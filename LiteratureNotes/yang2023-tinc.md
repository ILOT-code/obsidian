---
zotero-key: 4XIGHJ9W
zt-attachments:
  - "75"
title: "TINC: Tree-structured Implicit Neural Compression"
authors:
  - Runzhao Yang
  - Tingxiong Xiao
  - Yuxiao Cheng
  - Jinli Suo
  - Qionghai Dai
doi: 10.48550/arXiv.2211.06689
conference: xxx
citekey: yang2023-tinc
tags:
  - ObsCite
---
# TINC: Tree-structured Implicit Neural Compression

**文章链接**: [Zotero](zotero://select/library/items/4XIGHJ9W) 
**网页链接**: [URL](http://arxiv.org/abs/2211.06689)
## Abstract

>[!abstract]
>Implicit neural representation (INR) can describe the target scenes with high fidelity using a small number of parameters, and is emerging as a promising data compression technique. However, limited spectrum coverage is intrinsic to INR, and it is non-trivial to remove redundancy in diverse complex data effectively. Preliminary studies can only exploit either global or local correlation in the target data and thus of limited performance. In this paper, we propose a Tree-structured Implicit Neural Compression (TINC) to conduct compact representation for local regions and extract the shared features of these local representations in a hierarchical manner. Specifically, we use Multi-Layer Perceptrons (MLPs) to fit the partitioned local regions, and these MLPs are organized in tree structure to share parameters according to the spatial distance. The parameter sharing scheme not only ensures the continuity between adjacent regions, but also jointly removes the local and non-local redundancy. Extensive experiments show that TINC improves the compression fidelity of INR, and has shown impressive compression capabilities over commercial tools and other deep learning based methods. Besides, the approach is of high flexibility and can be tailored for different data and parameter settings. The source code can be found at https://github.com/RichealYoung/TINC .

## Comments

### 前置工作

> [!note] Page 1
> 
> Preliminary studies can only exploit either global or local correlation in the target data and thus of limited performance.
> 
> ---
> 先前研究的不足：只能利用局部或者全局的相关性，因此性能受限的
> ^NCQHM3P8aFTQPBNK3p1

> [!note] Page 1
> 
> Two pioneering works using INR for data compression, including NeRV [7] and SCI [41], have attempted to handle this issue in their respective ways.
> 
> ---
> 前置工作
> NeRV：为MLP引入卷积
> SCI：分割数据，减小数据频谱范围以被MLP覆盖
> ^ITYDXCRYaFTQPBNK3p1

> [!note] Page 2
> 
> introducing convolution operations
> ^CIFRHHRMaFTQPBNK3p2

> [!note] Page 2
> 
> positional encoding
> 
> ---
> INR中的参数共享方式，卷积和位置编码
> ^4WSATG32aFTQPBNK3p2

### 橙色

> [!note] Page 1
> 
> Specifically, we first draw on the idea of ensemble learning [12] and use a divideand-conquer strategy [20, 41] to compress different regions with multiple MLPs separately.
> 
> ---
> 灵感来源：分治策略以及集成式的INR
> ^XL8VEKNYaFTQPBNK3p1

### 黄色

> [!note] Page 1
> 
> However, limited spectrum coverage is intrinsic to INR, and it is non-trivial to remove redundancy in diverse complex data effectively.
> 
> ---
> INR的频谱覆盖范围是有限的
> ^XF792Q48aFTQPBNK3p1

> [!note] Page 2
> 
> Hence, the optical flow algorithms that are widely applicable in video compression do not work well on such inherently spatially structured data [41],
> 
> ---
> 基于人眼光学的压缩算法，无法在医学数据上表现良好
> ^N8CGLRHZaFTQPBNK3p2

### 灰色

> [!note] Page 4
> 
> a larger L means finer partitioning with a larger number of blocks sharing parameters (with the same hyper layers as in the ancestor nodes), which is beneficial for describing the regions with rich details and thus improve the compression fidelity. On the other hand, with the same total number of parameters, a larger L will lead to more leaf nodes K and less parameters |Θk| each fk, which might reduce fk’s representation capability and might harm the compression fidelity in turn
> 
> ---
> L的平衡：大的L带来更精细的划分，但每一个叶节点的表达能力下降
> ^WZIX5HRHaFTQPBNK3p4

> [!note] Page 4
> 
> the most valuable information in neuronal data is often distributed in sparser regions.
> 
> ---
> 稀疏的区域可认为更重要
> ^CIGY6ABGaFTQPBNK3p4

### 绿色

> [!note] Page 1
> 
> we propose a Tree-structured Implicit Neural Compression (TINC) to conduct compact representation for local regions and extract the shared features of these local representations in a hierarchical manner.
> 
> ---
> 解决思路：树形INR结构，以层级结构来共享参数
> ^QK9ASF5IaFTQPBNK3p1

> [!note] Page 3
> 
> Specifically, we adopt the divide-and-conquer strategy as in [12, 20, 41] to ensemble all implicit functions {fk, k = 1, · · · , K} that represents data at its corresponding coordinate regions {Vk, k = 1, · · · , K} and compose an f
> 
> ---
> 分治策略
> ^KVAW33YGaFTQPBNK3p3

> [!note] Page 4
> 
> The parameters at the shallow level describe the shared features at large scale
> 
> ---
> 越浅层的参数，越描述全局特征
> ^HCF7D5EPaFTQPBNK3p4

### 蓝色

> [!note] Page 1
> 
> Extensive experiments show that TINC improves the compression fidelity of INR, and has shown impressive compression capabilities over commercial tools and other deep learning based methods.
> 
> ---
> 实验结论
> ^SBCQN8BMaFTQPBNK3p1

> [!note] Page 4
> 
> Therefore, for data with high global redundancy or repetitiveness, i.e., high non-local similarity among sub-regions at large scales, allocating more parameters to the shallow level will be more beneficial to improve its compression fidelity.
> 
> ---
> 层间参数分配：对于全局冗余高的图片，分配给浅层的参数多
> ^4ECIM8F6aFTQPBNK3p4

> [!note] Page 4
> 
> If we allocate more parameters to the more important regions, then we can sacrifice the unimportant regions in exchange for the improvement of compression fidelity of the important ones.
> 
> ---
> 层内参数划分：给信息多的区域分配多的参数。
> ^UNBEGLA7aFTQPBNK3p4

> [!note] Page 6
> 
> In other words, the benefit of using a large tree is more significant when the target data contains more high-frequency information.
> 
> ---
> 对于复杂数据，也就是包含更多高频信息的数据，树层次越多越好
> ^GS7F5CBPaFTQPBNK3p6

> [!note] Page 7
> 
> The results show allocating more parameters to the shallow level is more likely to improve fidelity when the global consistency of the data is greater than 0.7, and allocating more to the deep level is better when the global consistency lower than 0.6. Otherwise, an even allocation may be a better choice.
> 
> ---
> 🔤结果表明，当数据的全局一致性大于0.7时，向浅层分配更多的参数更有可能提高保真度，而当全局一致性低于0.6时，向深层分配更多参数更好。否则，平均分配可能是更好的选择。🔤
> ^9WTGUA5YaFTQPBNK3p7

### 紫色

> [!note] Page 4
> 
> In Sec. 5.3, we experimentally prove that it is favourable using large tree levels for the data with rich details or under low compression ratios.
> 
> ---
> L的分配：细节多的图片或者要求低压缩率的情况下，L大点好
> ^ZVD57PZUaFTQPBNK3p4

> [!note] Page 8
> 
> We found that importance based allocation can significantly improve those regions that have very poor compression fidelity under even allocation.
> 
> ---
> 我们发现，基于重要性的分配可以显著改善那些在均匀分配下压缩保真度非常差的区域。
> ^9RCJX3IDaFTQPBNK3p8

### 品红

> [!note] Page 3
> 
> Specifically, for a leaf node at level l, its corresponding MLPimplemented fk’s hidden layers can be divided into l segments, i.e. fk = f out k ◦ fl k ◦ f l−1 k ◦ ··· ◦ f1 k ◦ f in k . For a set of leaf nodes, we determine the number of shared segments based on the number of their common ancestor nodes.
> 
> ---
> 参数共享策略
> ^94J65WP8aFTQPBNK3p3

> [!note] Page 5
> 
> We evaluated our approach on both massive medical and biological data. For the former we used HiP-CT dataset [38],
> 
> ---
> 🔤我们根据大量的医学和生物学数据对我们的方法进行了评估。对于前者，我们使用了HiP-CT数据集[38]，🔤
> ^N78VNQFUaFTQPBNK3p5

> [!note] Page 5
> 
> For the latter we used the sub-micrometer resolution mouse whole-brain microscopic data (Neurons and Vessels) captured by our self-developed imaging system,
> 
> ---
> 数据集
> ^YR8LYGV3aFTQPBNK3p5



## Method(s)

### 集成INR

$$  
\begin{aligned} f(\mathbf{v};\mathbf{\Theta})&:=\sum_{k=1}^K\mathbf{1}_{\mathbf{V}_k}(\mathbf{v},f_k(\mathbf{v},\mathbf{\Theta}_k)),\\ \mathbf{1}_{\mathbf{V}_k}(\mathbf{v},x)&=\begin{cases}x,\mathbf{v}\in\mathbb{V}_k\\0,else\end{cases}. \end{aligned}  
$$

  

对于某个叶子节点区域的点，使用其对应的MLP来编解码。

### 树形结构

以8分树来分解原图像。 ![[Pasted image 20231016205353.png]]

> We let these {f_k} share their neural network parame- ters hierarchically with each other according to thc spa- tial distance between corresponding regions \mathbb{V}_k. The shar- ing mechanism is defined on the octree structure. Specif- ically, for a leaf node at level l, its corresponding MLP- implemented f_k's hidden layers can be divided into l segments, i.e. f_k=f_k^{out}\circ f_k^l\circ f_k^{l-1}\circ\cdots\circ f_k^1\circ f_k^{in}. a set of leaf nodes, we determine the number of shared segments based on the number of their common ancestor nodes. For example, if f_i and f_j share the same ancestor nodes at 1\sim3 levels, three pairs of hidden layer segments (f_i1,f_j1),(f_i2,f_j2),(f_i3,f_j3) will share the same parameters.

## Conclusion

- 树的层树：图像的复杂度越高（高频成分多）或者压缩率低的情况下，层数大点好。
    
- 层间参数分配：浅层参数的增加，能显著增加距离较远但相似程度高的叶子节点的解压缩相似度。在全局相似度较高时，可以增加浅层参数量。
    
- 层内参数分配：给细节丰富度高的节点（密度大）分配更多参数。