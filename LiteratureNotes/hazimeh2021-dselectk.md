---
zotero-key: DFAT6T8W
zt-attachments:
  - "141"
title: "DSelect-k: Differentiable Selection in the Mixture of Experts with Applications to Multi-Task Learning"
authors:
  - Hussein Hazimeh
  - Zhe Zhao
  - Aakanksha Chowdhery
  - Maheswaran Sathiamoorthy
  - Yihua Chen
  - Rahul Mazumder
  - Lichan Hong
  - Ed H. Chi
doi: 10.48550/arXiv.2106.03760
conference: NIPS2021
citekey: hazimeh2021-dselectk
tags:
---
# DSelect-k: Differentiable Selection in the Mixture of Experts with Applications to Multi-Task Learning

**文章链接**: [Zotero](zotero://select/library/items/DFAT6T8W) [attachment](<file:///home/ilot/Zotero/storage/I5NQ7X3N/Hazimeh%20%E7%AD%89%20-%202021%20-%20DSelect-k%20Differentiable%20Selection%20in%20the%20Mixture.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2106.03760)
## Abstract

>[!abstract]
>MoE架构在提升网络的容量和在多任务学习中改善参数共享方面展现了巨大的潜力。但如今，最先进的MoE结构（如Top-K gating）是不光滑的的，这影响了收敛的性能，参数会出现振荡的现象。
>本文提出了DSelect-k, 一种可微的、稀疏的门控结构，能够使用一阶的优化方法来进行优化，在多个数据集合上表现的比Top-K更好。

## Comments


## MoE中的门结构--历史
MoE结构具有门控结构和一系列专家网络，网络的输出由它们共同组成：
$$
\sum\limits_{i=1}^{i=n}f_{i}(x)g(x)_i
$$
门控结构 $g(x)_i$ 有一些经典的做法：
1. Softmax Gate: 采用 Softmax 函数生成一维概率向量 : $\sigma(Ax+b)$
2. Top-K Gate：是 Softmax 结构的一种稀疏化变体，$\sigma(KeepTopK(Ax+b))$, $KeepTopK(t)$ 把向量 $t$ 的前 K 大元素保持不变，其它的置 0，这种方法有助于训练很多个专家，因为在前向和反向过程中它仅仅选取了部分的专家。

本文提出的 DSelect 和 Tok-k 类似，根据用户选取的 k 来稀疏的选取一些专家，并且此结构是光滑的，参数量和计算量也会更少。

![[Pasted image 20240406151600.png]]



## Differentiable and Sparse Gating

本文需要解决的问题是：在 $n$ 个专家网络中，如果选取不超过 $k$ 个网络，来使得损失函数最小：
$$\begin{aligned}\min_{f_1,...f_n,w}~~&\frac1N\sum_{(x,y)\in\mathcal{D}}\ell\Big(y,\sum_{i=1}^nf_i(x)w_i\Big)\\\text{s.t.}~~~&\|w\|_0\leq k\\&\sum_{i=1}^nw_i=1, w\geq0.\end{aligned}$$

$w$ 是一个概率向量，满足大于等于 0，和为 1，非零元素个数小于等于 k 这三个限制。

这个带有约束的问题是不能够直接使用梯度下降来优化的，后面会对此进行一步步的变换，把这个带有约束的问题变成一个无约束的问题，并保证 $w$ 的这 3 个性质。

### 只选择 1 个专家的情况

在只选择一个专家的情况下，$w$ 是一个 one-hot 向量。
设有 $n$ 个专家需要选择，用一个长度为 $m(m=log_{2}n)$ 二元数 $(z_{m}z_{m-1}...z_1)$ 去表示 $w$ 中哪个元素是 1.
令 $l=z_{m}z_{m-1}...z_1$, 则 $w$ 中第 $l+1$ 个数 (从 1 开始数)为 1，其它数为 0。

那么，在已知 $(z_{m}z_{m-1}...z_1)$ 的情况下，如何使用具体的运算生成这个 $w$ 呢，可以根据位运算去实现。

>[!instance]
>假设 $n=4$，则 $m=2$,
>那么 $w$ 可以这么根据下标的二进制串，写成：$w=[\bar{z_1}\bar{z_2}, z_1\bar{z_2}, \bar{z_1}z_2, z_1z_2]^T$
>当 $z_1,z_2$ 确定时，只有对应那个值为 1，其它为 0

把这个例子推广到专家数量任意的情况，可以写为：
$$w_i=r(z)_i=\prod_{j\in\mathcal{B}(i-1)}(z_j)\prod_{j\in[m]\setminus\mathcal{B}(i-1)}(1-z_j)$$
其中 $\mathcal{B}(t)$ 表示数字 $t$ 的二进制串中为 1 的那些位置，比如 $\mathcal{B}(3)=\{1,2\}$

这样，$r(z)$ 就是满足概率单纯形的。

### 推广到多专家的情况
上文中，一组 $m$ 个二进制数就生成了对应了选择一个专家的概率向量，如果要选择 $k$ 个专家，显然可以准备 $k$ 组这样的二进制数，但这样它们的和就不是 1 了，而是 $k$，可以对每一组数乘一个概率值即可：
$$q(\alpha,Z)=\sum_{i=1}^k\sigma(\alpha)_ir(z^{(i)}),$$

其中，$\alpha\in\mathbb{R}^k$，它经由 softmax 生成每一组的权重，$Z\in \mathbb{R}^{k \times m}$，表示 $k$ 组二进制数。
在这样的情况下，$q(\alpha,Z)$ 依旧是概率单纯形

于是，面对的问题从之前的有约束问题，变成了下面这个无约束的问题：
$$\begin{aligned}\min_{f_1,...f_n,\alpha,Z}&\frac1N\sum_{(x,y)\in\mathcal{D}}\ell\Big(y,\sum_{i=1}^nf_i(x)q(\alpha,Z)_i\Big)\\&z^{(i)}\in\{0,1\}^m, i\in[k]\end{aligned}$$

而且，可以证明，这两者是等价的，可以容易地从一个问题的解构造出另一个问题的解。

### 光滑化
前文中，$z$ 只能取 $\{0,1\}$, 这并不好，因此将它松弛到 $(-\infty,+\infty)$，并使用一个函数进一步处理：
$$S(t)=\begin{cases}0&\text{if} t\leq-\gamma/2\\-\frac{2}{\gamma^3}t^3+\frac{3}{2\gamma}t+\frac{1}{2}&\text{if} -\gamma/2\leq t\leq\gamma/2\\1&\text{if} t\geq\gamma/2\end{cases}$$

它和 sigmod 类似，但能够精确输出 0 和 1，连续可微。

因此，优化函数被写为：
$$
\begin{align*}
\min_{f_1,...f_n,\alpha,Z}\quad\frac1N\sum_{(x,y)\in\mathcal{D}}\ell\Big(y,\sum_{i=1}^nf_i(x)\tilde{q}(\alpha,Z)_i\Big).\\\\
\tilde{q}(\alpha,Z):=q(\alpha,S(Z))=\sum_{i=1}^k\sigma(\alpha)_ir\big(S(z^{(i)})\big),
\end{align*}
$$
现在，再来看看 $r\big(S(z^{(i)})\big)$ 这个概率向量依旧是概率单纯形。
首先，$r\big(S(z^{(i)})\big)$ 中的每一个元素都大于 0，这很容易证明，接着证明其和为 1.
也就是证明对任意 $x\in [0,1]^t$,  $\sum\limits_{i=1}^{i=2^t}r(x)_i=1$
显然，当 $t=1$ 时，$r(x)_{1}= 1-x_1$, $r(x)_2=x_1$，得证。使用归纳法继续证明：
设该结论对 $t$ 成立，证明对 $t+1$ 成立：
$$r(x)_i=\left\{\begin{matrix}r([x_1,x_2,\ldots,x_t])_i(1-x_{t+1})&i\in[2^t]\\r([x_1,x_2,\ldots,x_t])_{i-2^t}x_{t+1}&i\in[2^{t+1}]\setminus[2^t]\end{matrix}\right.$$

那么
$$
\begin{align*}
\sum\limits_{i=1}^{i=2^{t+1}}r(x)_i&=\sum\limits_{i=1}^{i=2^{t}}r(x)_{i}+ \sum\limits_{i=2^t+1}^{i=2^{t+1}}r(x)_{i}\\\\
&=\sum\limits_{i=1}^{i=2^{t}}r([x_1,x_2,\ldots,x_t])_i(1-x_{t+1}+x_{t+1})\\\\
&=1
\end{align*}
$$

得证。

可是，它并不满足最后一条限制，就是概率向量中非 0 元素的数量不超过 $k$.

观察 $S$ 这个函数，它在值为 0 和 1 的时候的梯度为 0，有前面的证明 $r(S(z))$ 的和为 1，只有给与充分长的时间，这个概率向量可以收敛到 one-hot 的形式，进而满足非零元素数量小于等于 $k$.

为了加速这一收敛过程，作者加了一个正则项：
$$
\begin{align*}
\min_{f_1,...f_n,\alpha,Z}&\quad\sum_{(x,y)\in\mathcal{D}}\frac1N\ell\Big(y,\sum_{i=1}^nf_i(x)\tilde{q}(\alpha,Z)_i\Big)+\lambda\Omega(Z)
\\\\
&\Omega(Z) := \sum_{i=1}^{k}h\big(r(S(z^{(i)}))\big) \text{a}
\end{align*}
$$
其中 $h(.)$ 计算输入概率向量和 one-hot 向量之间差距 (熵)。

### 和输入样本相关的路由结构

前文介绍的结构是静态的，和输入无关，也可以写成和输入相关的形式：
$$
\begin{align*}
v(G,W,x)=\sum_{i=1}^k\sigma(Gx)_ir\big(S(W^{(i)}x)\big).\\
\sum_{(x,y)\in\mathcal{D}}\left(\frac1N\ell\Big(y,\sum_{i=1}^nf_i(x)v(G,W,x)_i\Big)+\lambda\Omega(W,x)\right),
\end{align*}
$$
## Experiments