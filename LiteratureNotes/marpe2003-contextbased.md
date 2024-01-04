---
zotero-key: D7BVA9ZX
zt-attachments:
  - "384"
title: Context-based adaptive binary arithmetic coding in the H.264/AVC video compression standard
authors:
  - D. Marpe
  - H. Schwarz
  - T. Wiegand
doi: 10.1109/TCSVT.2003.815173
conference: xxx
citekey: marpe2003-contextbased
tags: []
---
# Context-based adaptive binary arithmetic coding in the H.264/AVC video compression standard

**文章链接**: [Zotero](zotero://select/library/items/D7BVA9ZX) 
**网页链接**: [URL](https://ieeexplore.ieee.org/document/1218195?denied=)


CABAC 是在 H.264中用来编码 syntax element 的编码器。它是对算术编码的改进，能够根据上下文自适应改进，同时缓解了算术编码需要大量乘法运算的问题。

CABAC 由三个环节组成：
1.binarization; 
2.context modeling;
3.binary arithmetic coding.

首先将语法元素编码成二进制串。然后选择 regular 模式还是 bypass 模式。在 regular 模式下，根据上下文建模，并自适应的来更新模型。
![[Pasted image 20240103200045.png]]
## Binarization
将待编码串转化成 01 串。
下面是所谓 UEG0 二值化方法，联合使用 TU 和 EG0 编码方法。
![[Pasted image 20240103200412.png|500]]

## Context Modeling

由于已经转化成了 01 串，后面的编码在二元算术编码的基础上扩展而来。
算术编码需要不断地对一个间隔 $R$ 进行划分，由此产生大量乘法运算 $R\times p$。解决这个问题的思路是对 $R$ 和 $p$ 都进行量化：$\boldsymbol{Q}=\{Q_0,\ldots,Q_{K-1}\},\boldsymbol{P}=\{p_0,\ldots,p_{N-1}\}$，然后维护一张 $K\times N$ 的表即可。
在 CABAC 中，$N$ 取为了 64，因此：
一个概率模型由以下两个部分组成：
1. 一个 6 bit 的概率状态 $\sigma_{\gamma}$ ，表征了 LPS 的概率
2. 一个 1 bit 的表征 MPS (概率最大的那个值，0 或 1)的值 $\varpi_{\gamma}$.

LPS 的概率小于 0.5，被限定在了 $[0.01875,0.5]$ 范围内。
$$
\begin{gathered}
p_{\sigma}= \alpha\cdot p_{\sigma-1}\mathrm{~for~all~}\sigma=1,\ldots,63 \\
\mathrm{with}~\alpha= \left(\frac{0.01875}{0.5}\right)^{1/63}\mathrm{~and~}p_0=0.5. 
\end{gathered}
$$
**自适应状态更新**
$$
 \left. p_{\mathrm{new}}=\left\{\begin{array}{ll}\max (\alpha\cdot p_{\mathrm{old}}, p_{62}),&\text{if a MPS occurs}\\\alpha\cdot p_{\mathrm{old}}+(1-\alpha),&\text{if a LPS occurs}\end{array}\right.\right. 
$$
对于 MPS 的情况，可以有个简单的更新公式：$max(\sigma,62)$ ，LPS 的情况可以用一个表来记录。
![[Pasted image 20240104115242.png|500]]

另一个部分是对于 $R$ 的量化，在 CABAC 中，每一步的 $R$ 都被限定成了一个 9 位的二进制数，也就是范围在 $[2^8,2^9)$，并进一步的将 $R$ 量化成 4 个离散值。因此 regular 模式下，流程如下：

![[Pasted image 20240104120358.png|500]]


如果发现 0 和 1 的概率相近，为了加速编解码过程，会选择 bypass 编码模式：
![[Pasted image 20240104123159.png]]

