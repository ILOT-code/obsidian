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
tags: []
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

另一些使用非均匀量化，LUT-GEMM 把全精度的参数分解为一个二元数和一组单独的缩放因子