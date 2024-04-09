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

全局、局部剪枝：全局剪枝比较所有的修剪单元，他可能会导致特定区域过于稀疏。局部剪枝对每个区域的稀疏度作约束。

是否数据驱动：数据驱动的剪枝依靠可使用的数据来获得剪枝策略，data-free 的方式独立于数据。

上游、下游剪枝：上游剪枝在微调之前进行，下游剪枝在微调时进行。前者保留了模型对多任务的适用性，后者则专注于特定的任务。

### Pruning Methods for Medium-Size Language Models

#### Unstructured Pruning for Medium-Size Language Models

**Magnitude-based Pruning**
有研究表明，在 BERT 中，有 30%到 40%的权重是不必要的，删减他们不会造成性能损失。另外，对于 BERT 而言，针对特定任务进行的微调对提升稀疏率没有帮助，因此，Once for All 在微调之前对 BERT 进行剪枝，然后应用到各个子任务上。

此方法直接剪去权重，可能会造成性能的快速下降，因此一些研究设计了一些控制稀疏率的策略，逐步地降低稀疏率。

**Loss-based Pruning**
magnitude 本身可能无法准确反应出权重的重要性，小的权重也可能有用，loss-based 方法由此而生。
最基本的方法使用下面这个式子：
$$\mathbf{I}=-\mathbf{w}\nabla\mathcal{L}(\mathbf{w})$$
有两种对于此表达式的解释：1，泰勒展开一阶项。2，权重的负梯度方向表示权重想要增加的方向。因此，如果权重方向与权重增长方向一致，则表明该权重在特定任务中的重要性，因为该任务需要其大小持续增加。

另一种类别集成了二阶导信息：
$$\mathbf{I}=\frac12(\mathbf{w}-\mathbf{w}^*)^\top\mathbf{H}_\mathcal{L}(\mathbf{w}^*)(\mathbf{w}-\mathbf{w}^*).$$
(在训练的末尾中，一阶导被认为接近 0 而被忽略了。)
OBD和OBS两者都利用损失函数的 Hessian 矩阵来选择性地消除特定参数，同时最大限度地减少对损失的影响。为了简化计算，两种方法都在一定程度上简化了 Hessian 矩阵的计算。

**Regularization**
L1 正则化具有导致权重稀疏的额外效果。然而，当涉及到直接修剪网络时，L1 正则化并不总是最合适的选择，这是因为 L1 正则化对较大权重施加了更实质性的惩罚。更常用的是 L0 正则化。
$$\min_{\mathbf{w},\mathbf{z}}\frac1N\sum_{i=1}^N\ell(\mathbf{w}\odot\mathbf{z};(x_i,y_i))+\lambda\|\mathbf{w}\|_p,$$
掩码 $z$ 是二元的，给梯度下降带来一定困难。一种解决方法是把 $z$ 的概率分布进行近似：
$$\begin{aligned}
&u\sim\mathcal{U}(0,1), \\
&\begin{aligned}s=\text{Sigmoid}((\log u-\log{(1-u)}+\log\alpha)/\beta)\end{aligned} \\
&\bar{s}=s(\zeta-\gamma)+\gamma, \\
&\begin{aligned}z=\min(1,\max(0,\bar{s})),\end{aligned}
\end{aligned}$$
其中，$\alpha$ 是每个位置上代训练的参数，其它参数是超参数。在 $\alpha=0$ 时，这种分布把一半的概率密度放在离散的 0 和 1 上，剩下的一半密度均匀放在 0，1 之间。
![[Pasted image 20240408141241.png|500]]
在训练完成之后，设定一个阈值，低于这个阈值的 $z$ 被置 0.

#### Structured Pruning for Medium-Size Language Models
**Magnitude-based**
把结构中所有参数的 magnitude 相加起来作为整个结构的 magnitude，然后用类似非结构的方法进行剪枝。
**Loss-based Pruning**
对注意力头的修剪：Michel 提出更具掩码 $z$ 的梯度来计算注意力头的分数，从而去除一些头，结果表明大概 20%~30%的注意力头是没有用的。DSP 方法将注意力头的修剪看成子集选取问题，实验结果表明可以修剪 90%的注意力头。
**Regularization**
可以在其他方法的基础上，引入正则化项。

### Pruning Methods for LLMs
#### Unstructured Pruning for LLMs
Wanda 和 SparseGPT 是这方面的领先方法。
**Magnitude-based**
在中形模型中，直接衡量权重的 magnitude，但这种做法在 LLM 中效果并不好, 一般使用权重和激活值结合起来来计算 magnitude。
Wanda 提出了这样的计算 magnitude 的方法，考虑一个线性层，权重的分数是：
$$\mathbf{S}_{ij}=|\mathbf{W}_{ij}|\cdot\|\mathbf{X}_j\|_2,$$
设 $W$ 的维度是 $(cout,cin)$, $X$ 的维度是 $(cin,N\times L)$, 那么每个 $w_{ij}$ 的分数需要乘以输入矩阵的第 $j$ 行的 2 范数。

**Loss-based**
SparseGPT 是一种二阶剪枝方法。 SparseGPT 剪枝方法由两个主要部分组成：掩模选择和权重重建过程。最初，掩码选择基于度量（例如权重大小）来识别用于修剪的权重。随后，对未剪枝的权重使用 OBS 的方法优化以补偿剪枝的权重。 

#### Structured Pruning for LLMs
结构化的剪枝有利于实现硬件加速，但由于性能会更快的下降，在这种方法中一般会用到微调。LLM-Pruner 是这个领域的 baseline。
**Magnitude-based***
一般以行或列视为剪枝单元。FLAP 就以列为单元，其 magnitude 的计算方法是输入样本的该特征的方差，再乘以权重举证对应列的 2 范数。
**Loss-base**
在 LLM 中，此类方法避免预定义剪枝单元，而是动态的识别它们。
LLM-Pruner 自动地识别和提取这样的组合结构 (依据神经元的连接性)。一个权重的重要性使用二阶泰勒展开来衡量，整个组的重要性通过求和、相乘或其他方法生成。LLM-Pruner 在微调过程中使用了 LoRA。实验表明，在减去 20%的参数时，保持了大部分的性能。

与手动设计修剪的度量特征相反，Ji 采用非神经模型，而是梯度提升决策树（GBDT）作为准确度预测器。使用该准确度度预测器可以进一步优化搜索空间和搜索过程，以自动识别最佳修剪模型。（通过将 GBDT 训练为准确度预测器，模型能够评估和预测不同剪枝配置对神经网络精度的影响。）

## KNOWLEDGE DISTILLATION

知识蒸馏将复杂的教师模型获得的知识转移到简单的学生模型当中。

### Basic Concepts
KD 有 4 个种类：
![[Pasted image 20240409104417.png|500]]
logit-based KD 比较它们 softmax 层的差异，Hilton 提出的损失函数是：
$$L=\alpha\cdot L_D(p(z_t,T),p(z_s,T))+(1-\alpha)\cdot L_S(y,p(z_s,T))$$
前一项比较教师和学生的 softmaxd 的输出，后一项比较学生网络和标签，$L_D,L_S$ 都是交叉熵损失函数。

Feature-basednKD: 对于教师和学生的各个中间层的特征进行比较，希望让学生网络理解潜在的过程：
$$L_{feature}=\mathcal{L}((f_t(x),r(f_s(x)))$$
其中 $r()$ 用来把学生网络的特征映射到和教师一样的形状。  FitNet 使用 MSE 和可学习的 $r$ 来进行训练。

Relation-based KD: relation 表现在两方面：1，同一样本不同层次的输出的关系。2，不同样本输出之间的关系。
$$L_{response}=\mathcal{L}((f_t(t_i,t_j),f_s(s_i,s_j))$$
其中 $t_i,t_j,s_i,s_j$ 分别表示来自老师和学生的同一样本不同层或不同样本的输出，$f$ 函数用来计算相似性。FSP 使用 Gram 矩阵作为 $f$，MSE 作为 $\mathcal{L}$.

Black-box KD: 很多闭源大模型无法获取中间层的信息，智能通过对最终预测结果进行学习。

### KD for Medium-Size Language Models
KD 可以在模型预训练之后进行，也可以在微调之后进行。

#### 微调之后
PKD 方法提出了两种方法，1，学生学习每 k 层。2，学生学习最后 k 层。实验证明前者更好。
DynaBERT 则结合了剪枝的思想。它由两部分构成，首先设定一个 $m_w\in(0,1)$, 保留最重要的 $m_w$ 的参数，作为 1 号网络的初始化，然后进行学习。之后，设定参数 $m_d\in(0,1)$，从 1 号网络均匀地选取 $m_d$ 层作为 2 号网络的初始化，最后把 1 号网络的知识传给 2 号网络。
为了使教师能够更好地和学生交互，Metadistil 提出：在训练数据上蒸馏出学生模型，然后再测验数据上，使用学生模型的输出来更新教师模型，以让教师模型能够更好教学，最后继续蒸馏出学生模型。
AdaBERT 实现了学生网络结构的自适应变化，它把整体结构看成一系列 cell 堆叠而成，每个 cell 都是一个 DAG，在确定了 cell 的层数后，搜索空间就是 DAG 中各个点的连接操作，候选的操作有 pooling，卷积，indentity, skip。并且在损失函数中，加入了模型的复杂度的。
Meta-KD 认识到学生模型可以在其他辅助的教师模型（其他域）中获得更好的学习。

#### 预训练之后
MiniLM 把蒸馏的重心放在最后一层 transformer 上，它利用这一层的 v 矩阵来得到知识并蒸馏，能够把教师网络先蒸馏到一个隐藏维度更低的网络，再进一步蒸馏出学生网络。
TED 为每一层装备了一个任务感知的过滤器，来从隐藏层中提取知识。

### KD for Large Language Models
许多大预言模型是闭源的，因此几乎都是使用黑盒蒸馏。这些黑盒方法利用大模型的指令跟踪、思维链和上下文学习的能力进行设计。

#### Instruction-Following
指令跟踪能力是指 LLM 可以根据特定的指令 (告诉模型该完成什么样的任务 )和输入数据得到相应的输出。
SELF-INSTRUCT 提出了一种自我蒸馏的方法，模型既是老师，又是学生。首先，它手工设计一个小规模的任务池，每一个任务由一条指令和输入构成。然后，利用这个任务池，让模型生成新的指令和对应的输入输出，将它们进行过滤后，放回任务池，重复迭代，生成一个巨大的数据集，以此来微调学生网络。
这种方法在数据集的创建过程中，没有学生网络的参与。Lion 采用了对抗性的方法，由裁判模块进行判断，识别学术网络对哪些指令学习的不好，促使教师网络生成新的那些指令。

#### Chain-of-Thought
思维链指 LLM 能够根据提示中的原理来生成更好回答的能力。CoT 的典型范例是利用大模型来生成包含基本原理的强化版数据集，将其用于微调学生模型。研究的重点是如何生成这样强化版的数据集以及学术网络如何利用这些基本原理。
CROP：对于一个包含问题和答案的数据集，让教师模型生产问题对应的回答和解释，如果回答正确，则保留其解释，如果不正确，教师模型根据正确答案生成解释。
SOCRATIC CoT 从数据集中选择一部分，手工把这些问题分解成一些子问题，来让 LLM 生成原理，并进一步回答剩下的问题。然后进行过滤，生成强化的数据集。最后训练两个学生模型，一个提问一个回答。

#### In-Context Learning
指 LLM 在不更新参数的情况下，能够根据先前的输入为新输入生成正确输出的能力。

## COMPACT ARCHITECTURE DESIGN
紧凑架构设计旨在通过优化网络结构和算法，同时减少计算资源和内存的消耗，实现模型效率的大幅提升。将重点关注优化注意力计算和 Transformer 架构设计。

### Efficient Attention
self-attention 对于长度为 N 的序列，时空复杂度都是 N 的二次方，阻碍了对长序列的处理。

#### Sparse Attention

stride-based: 让每个 token 只关注前几个 token。早期的研究让每个位置的 token 只关注预设好的前面连续的几个 token，这种方法并不具有普适性。后面有研究让每个注意力头自主学习

![[Pasted image 20240409151327.png|500]]