---
zotero-key: IW2FDBIC
zt-attachments:
  - "145"
title: "Model Compression and Efficient Inference for Large Language Models: A Survey"
authors:
  - Wenxiao Wang
  - Wei Chen
  - Yicong Luo
  - Yongliu Long
  - Zhengkai Lin
  - Liye Zhang
  - Binbin Lin
  - Deng Cai
  - Xiaofei He
doi: 10.48550/arXiv.2402.09748
conference: xxx
citekey: wang2024-model
tags:
---
# Model Compression and Efficient Inference for Large Language Models: A Survey

**文章链接**: [Zotero](zotero://select/library/items/IW2FDBIC) [attachment](<file:///home/ilot/Zotero/storage/KV3YSMHP/Wang%20%E7%AD%89%20-%202024%20-%20Model%20Compression%20and%20Efficient%20Inference%20for%20Larg.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2402.09748)
## Abstract

>[!abstract]
>Transformer based large language models have achieved tremendous success. However, the significant memory and computational costs incurred during the inference process make it challenging to deploy large models on resource-constrained devices. In this paper, we investigate compression and efficient inference methods for large language models from an algorithmic perspective. Regarding taxonomy, similar to smaller models, compression and acceleration algorithms for large language models can still be categorized into quantization, pruning, distillation, compact architecture design, dynamic networks. However, Large language models have two prominent characteristics compared to smaller models: (1) Most of compression algorithms require finetuning or even retraining the model after compression. The most notable aspect of large models is the very high cost associated with model finetuning or training. Therefore, many algorithms for large models, such as quantization and pruning, start to explore tuning-free algorithms. (2) Large models emphasize versatility and generalization rather than performance on a single task. Hence, many algorithms, such as knowledge distillation, focus on how to preserving their versatility and generalization after compression. Since these two characteristics were not very pronounced in early large models, we further distinguish large language models into medium models and ``real'' large models. Additionally, we also provide an introduction to some mature frameworks for efficient inference of large models, which can support basic compression or acceleration algorithms, greatly facilitating model deployment for users.

## Comments


## QUANTIZATION

量化相比其它方法具有这些优点：
1. 高压缩比：例如葱 32 位浮点量化到 4 位的整性，直接就能压缩 8 倍。
2. 低成本：很多的量化方法都不需要对模型进行额外的训练。
3. 高度灵活性：与其它各种各样的方法兼容，可以进行组合。

### Basic Concepts
均匀量化：把实数量化到均匀的、有限的整数：
$$Q(r)=\text{ROUND}(\frac rS)+Z$$
非均匀量化：
$$Q(r)=Q_i,\text{ if }r\in[\Delta_i,\Delta_{i+1})$$
这种方式量化的间隔不一致。一般而言，模型的参数分布是不均匀的，因此合适的非均匀量化能够比均匀量化压缩效果更好，但是在量化和反量化的过程中需要进行额外的查表操作，费时且不利于并行性。

裁剪与校准：给原来的实值设定一个范围 $[\alpha,\beta]$，超过界限的值被映射到对应的临界点。校准指如何选定这个范围。在均匀量化中，给定了范围，那么量化的间隔就可以获得：
$$S=\frac{\beta-\alpha}{2^b-1}$$

对称与非对称量化：如果 $\alpha+\beta=0,Z=0$ 那么就是对称量化，否则是非对称的。不对称量化是更加紧凑的，如果某个激活值是进过 Relu 得到的，那么它非负，显然不对称量化能更好的进行压缩。在反量化中，对称量化计算量更小。

量化的粒度：从粗糙到细腻有下面的分级：
1. 分层：如果是卷积层那么所有的过滤器同时量化，如果是线性层，那么整个权重矩阵一起量化。
2. 分通道：卷积层就是单个过滤器同时量化。
3. 分行、列：线性层的权重矩阵按行或列的方式同时量化
4. 分 token：一个 token 的对应的输出一起来量化
5. 分组：选择一些连续的参数作为一组来量化

量化是否需要额外训练：
1. Post-Training Quantization, PTQ: 无需训练，量化后直接进行推理。
2. Quantization-Aware Training, QAT：需要训练，为了克服量化的不可导，一般使用 STE 操作。
在大模型的量化中，需要进行训练的量化方法一般被认为是难以接受的，因为成本太大了。

静态、动态量化：网络的权重和激活值是有很大区别的，前者在推理时是固定的，后者是未知的并且在变化。因此，前者可以静态计算出裁剪范围，对后者的量化就分为了两种：
1. 静态：在推理前，先输入一些用来校准的输入，以此来找到激活值的一些统计规律，以此确定量化的一些参数。或则量化参数在训练中通过学习得到。
2. 动态：在推理过程中计算量化参数，一般比静态方法性能更好，但当然会引入额外计算负担。

模拟、仅整数量化：判断的依据是量化后的数是否被直接使用了。对于模拟量化而言，量化后的值在使用前会先进行反量化，而仅整数量化则不需要，直接使用量化后的整数进行运算。后者在一些对低比特整数运算作了优化的特殊硬件结构上很有优势。

是否只量化权重：有些量化只针对权重，而另一些则对权重和激活值都进行量化。对激活值的量化会比权重的量化更敏感，所以一般量化激活值的比特数要多一点。对于前者，在使用权重乘以激活值之前，需要先进行反量化。

### Quantization Methods for Medium-Size Language Models
中型模型主要有 BERT、BART、GPT-2，对他们的量化以 QAT 为主，因为训练被认为是可接受的。

**QAT**方法
Q-BERT 使用 8 比特量化激活值，对权重的量化采用混合精度，对于那些具有更小特征值的 Hessian 矩阵的层，采用更激进的量化，比特数更少。
TernaryBERT 把权重限制在了 ${-1,0,-1}$ 三个值，激活值使用 8 比特量化，并采用知识蒸馏的方法，通过最小化学生网络和教师网络的激活值以及注意力得分之间的差异来训练。
BiBERT 完全二值化 BERT，权重、激活值全部使用 1 比特量化，并设计了一个 Bi-Attention 结构和 DirectionMatching Distillation 策略去提高性能。

**PTQ**方法
GOBO 将大部分的遵循高斯分布的权重使用非均匀的 3 比特量化，并把其它的那些值使用 FP 32 存储。
I-BERT 为各种非线性层设计了一种仅整数运算的近似方法，避免了浮点运算。


**量化生成式的中形语言模型**
生成式的模型的量化方法很稀少，关键的区别在于量化误差在逐个 token 的生成过程中累积，因此量化生成语言模型通常是一个更复杂的问题。

直接将 BERT-like 的量化方法应用在生产模型上会面临两个障碍：
1. 词嵌入的同质化：量化之后，词嵌入向量之间的差异很小。
2. 权重分布不同


### Post-Training Quantization for LLMs

PTQ 方法由于无需训练在大模型中更受欢迎


**Weight-Only Quantization.**
只对网络参数进行量化，此类量化方法一般属于模拟量化。
LLM 中的激活值的模式和中型模型是不同的，这为直接将前面的量化方法应用在 LLM 上带来困难。

一些研究直接使用均匀量化 LLM：ZeroQuant-V2 使用 8 比特来量化 OPT 和 BLOOM 权重时结果还行，但当减少到 4 比特时，出现了严重的性能下降，他们进一步设计一个低秩（节省存储空间）的矩阵来近似量化误差，使得 $\hat{\mathbf{E}}+\hat{\mathbf{W}}$ 能够更好地近似 $\hat{\mathbf{W}}$. 

另一些使用非均匀量化，LUT-GEMM 把全精度的参数分解为一个二元数和一组单独的缩放因子。SqueezeLLM 验证了 LLM 推理是受内存限制的，相对于其他神经网络，算术强度极低，它采用基于灵敏度的 k 均值质心作为量化权重值 (将权重的 Hessian 矩阵近似为灵敏度)。

这些方法都在致力于减小：$\mathrm{argmin}_{\hat{\mathbf{W}}}||\mathbf{W}-Q(\mathbf{W})||$，可是，由于网络的强非线性，权重的小误差不一定代表输出的小误差。因此，另一些方法使用一个校准集 $C$，并以指标：$\mathrm{argmin}_{{\hat{\mathbf{W}}}}||\mathbf{XW}-\mathbf{X}Q(\mathbf{W})||\quad\mathrm{for~}\mathbf{X}\in C$ 来设计量化操作。OBQ 在量化某一个权重时，会立马更新其它的未量化的权重，以此来补偿损失。GPTQ 把 OBQ 的想法推广到 LLM 中使用，它不会立马更新，而是使用了一种 LAZY 策略，这样就能够并行的量化权重，提高速度。 QuIP 定义了一系列来优化前面方程的自适应的量化方法，其中的最优方法 LDLQ 在校准集上根据向量的二阶矩阵的 LDL 分解来找到最佳的更新未量化权重的方法，QuIP是第一个使用 2 比特来量化依旧取得理想结果的方法。

**Weight + Activation Quantization.**

 首先，在 LLM 中，同时量化权重和激活值并不是一个平凡的问题，一个重要的原因是激活值中的那些“系统性异常值 systematic outliers”，对于模型的性能是十分重要的，如果裁剪范围选择不合理，造成这些异常值的丢失，会照常显著的性能下降。

因此，这种量化方法对异常值有特殊的处理。LLM. int 8 ()研究发现这种异常值是十分普遍的，存在于几乎所有的 token 中，但是这些异常值所在的维度却是有限（很少）的，因此，他们将维度进行分组，那些包含了系统异常值的维度使用高精度，而其他维度使用低精度：
$$\mathbf{X}_{f16}\mathbf{W}_{f16}\approx\sum_{\hat{d}\in O}\mathbf{X}_{f16}^{\hat{d}}\mathbf{W}_{f16}^{\hat{d}}+\mathbf{S}_{f16}\cdot\sum_{d\notin O}\mathbf{X}_{i8}^d\mathbf{W}_{i8}^d$$
近年来，FP4, FP8 等低精度浮点的数据形式得到了英伟达等硬件的支持，它可以被看成非均匀量化的扩展：为小的数提供高精度，为大值提供低精度。由于低精度浮点的范围更广阔，在应对这些“系统异常值”时具有天然优势。一些研究也表明，在量化 LLM 中的激活值时，FP8 比 INT8 更好。

另一种思路是对所有维度进行缩放，这样那些异常的维度会被缩小，这样就能更好的量化：
$$\mathbf{Y}=\mathbf{XW}=(\hat{\mathbf{X}}\mathrm{diag}(\mathbf{s}))\cdot(\mathrm{diag}(\mathbf{s})^{-1}\hat{\mathbf{W}})=\hat{\mathbf{XW}}$$
许多研究对缩放因子 $s$ 作了各种尝试，一种 $s$ 的表达式是：
$$\mathbf{s}_i=\max(|\mathbf{X}_i|)^\alpha/\max(|\mathbf{W}_j|)^{1-\alpha}$$
当然，有研究也使用学习的方法来确定 $s$，在校准集上进行学习：
$$\min_\mathbf{s}\mathbb{E}[||\mathbf{XW}-(Q(\hat{\mathbf{X}})Q(\hat{\mathbf{W}})+\hat{\mathbf{b}})||_2^2].$$


### Quantization-Aware Training for LLMs



## PRUNING
剪枝在 CNN 模型中有很好的效果，但在 LLM 领域，相比量化和蒸馏，并不稳健。剪枝效果降低的原因来自于微调过程。由于模型参数较多，微调成本较高，更难达到剪枝的全部效果。

### Basic Concepts

修剪单元：指剪枝过程中最小的剪枝单元，分为非结构化剪枝和结构化剪枝。前者关注的是单个权重，它不受网络结构的限制，能够获得更高的稀疏率，但其稀疏模式不规则，计算效率并没有显著提升。后者关注网络结构，如注意力头、隐藏维度等，为了避免模型性能崩溃，其稀疏率低于前者。
$$\begin{aligned}&\min_{\mathbf{w},\mathbf{z}}\mathcal{L}(\mathbf{w}\odot\mathbf{z};\mathcal{D})=\min_{\mathbf{w},\mathbf{z}}\frac{1}{N}\sum_{i=1}^{N}\ell(\mathbf{w}\odot\mathbf{z};(x_{i},y_{i})),\\&s.t.\quad\|\mathbf{z}\|_0\leq t,\end{aligned}$$

修剪的度量：magnitude-based pruning, 如根据权重的绝对值大小来衡量，低于阈值的权重被置 0。loss-based metric，根据修剪之后损失的增加来度量，为了节省计算资源，一般展开到一阶或二阶导来进行衡量。还有一些正则化的方法也能够剪枝，如 $L0，L1，L2$ 正则化。

静态、动态剪枝：区别在于剪枝是否依据输入的特征。静态剪枝有几种模式：
![[Pasted image 20240408114458.png|500]]

迭代、一次性剪枝：在 LLM 中，迭代很耗费时间，one-shot 的剪枝更多一点。

全局、局部剪枝：全局剪枝比较所有的修剪单元，他可能会导致特定区域过于稀疏。