---
zotero-key: J6TADE3V
zt-attachments:
  - "640"
title: Learning Transferable Visual Models From Natural Language Supervision
authors:
  - Alec Radford
  - Jong Wook Kim
  - Chris Hallacy
  - Aditya Ramesh
  - Gabriel Goh
  - Sandhini Agarwal
  - Girish Sastry
  - Amanda Askell
  - Pamela Mishkin
  - Jack Clark
  - Gretchen Krueger
  - Ilya Sutskever
doi: 10.48550/arXiv.2103.00020
conference: xxx
citekey: LearningTransferableVisual2021
tags: []
---
# Learning Transferable Visual Models From Natural Language Supervision

**文章链接**: [Zotero](zotero://select/library/items/J6TADE3V) [attachment](<file:///home/ilot/Documents/Zotero/storage/YCV3WJGW/Radford%20%E7%AD%89%20-%202021%20-%20Learning%20Transferable%20Visual%20Models%20From%20Natural%20Language%20Supervision.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2103.00020)
## Abstract

>[!abstract]
> CLIP 提供了图像与文本对齐的能力，能够把这两种差异极大的信息格式转化到一个相同的特征空间上，进而能够计算出单条文本与单个图像间的匹配程度。
> CLIP 这样的能力赋予了它 zero-shot 的特性：对于特定的下游任务（如图像分类），可以构造出一些提示词，让 CLIP 直接来预测该提示词和图像之间的匹配程度。
> 该模型在大多数任务上表现出非平凡的迁移能力，并且能够与完全监督的基线模型竞争，而无需任何特定数据集的训练。例如，在 ImageNet 上达到了原始 ResNet-50 的准确率，



## Method

![[Pasted image 20250306141048.png]]

在 pre-training 中，通过两个 encoder 把 $N$ 张图片和其对应的 $N$ 个文本映射到 $N$ 个向量中去，$I_{1},I_{2},\dots I_{N},T_{1},T_{2},\dots T_{N}$。它们的向量长度被映射的相同。使用余弦相似度来衡量这 $N^{2}$ 个样本的相似度。总共有 $N^{2}-N$ 个负样本和 $N$ 个正样本。以此来进行“无监督”的训练。

训练完毕后，倘若应用到图像分类任务中，使用 prompt：“A photo of a”和对应的标签组合生成一系列文本，比较它们和图片特征相似度，最大的那条文本就是分类的结果。

## 一些细节
数据：训练这个模型所需的数据十分庞大，好在这种 text-image 对的数据互联网上很多，openai 构建了一个包含 400 million 对的数据集。庞大的数据集也为他们声称的 zero-shot 能力带来了质疑，imagenet 才 1000 个类别，这么大的数据集肯定为这些类别提供了丰富的训练数据。

模型：有别于 CLIP，另一种模型是给定图片，精确的预测出它对应的文本。这类模型的训练难度很高，因为文本的形式多种多样。CLIP 这样的对比式的模型不管在训练难度还是 zero-shot 能力上，都更好。

Choosing and Scaling a Model：图像编码器是一个 ViT 加一连串的 self-attention. 文本 encoder 是一个 Transformer

## 实验
![[Pasted image 20250306144904.png]]
![[Pasted image 20250306144938.png]]