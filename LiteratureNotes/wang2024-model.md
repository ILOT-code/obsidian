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
1. 