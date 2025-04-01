## F 范数
考虑矩阵 $\mathbf{Q}, \mathbf{K} \in \mathbb{R}^{N\times D}$，如何选取 $\mathbf{Q,K}$ 的某些维度，而抛弃剩下的维度，使得 $\mathbf{QK^{T}}$ 的计算结果损失最小。即下面的优化问题：
$$
\begin{aligned}
\min_{\mathbf{S}}&\left\|\mathbf{Q}\mathbf{K}^T-\mathbf{Q}\mathbf{S}\mathbf{K}^T\right\|_F \\
\text{subject to}&~\operatorname{Trace}(\mathbf{S})=\lfloor(1-\lambda)D\rfloor\\&\mathbf{S}=\operatorname{diag}(s_1,s_2,\ldots,s_D),\text{ where }s_j\in\{0,1\}
\end{aligned}
$$
有 $A=\{i|s_{i}=1\}, B=\{i\}|s_{i}=0\}$, $A \cap B = \varnothing$
用 $\mathbf{q}_{i},\mathbf{k}_{i}$ 表示矩阵的第 $i$ 列。有：
$$
\begin{aligned}
\mathbf{QK}^{T}=\sum_{i=1}^{D}q_{i}k_{i}^{T} = \sum_{i\in A \cup B}q_{i}k_{i}^{T}\\
\mathbf{QSK}^{T} = \sum_{i\in A}q_{i}k_{i}^{T} \\
\end{aligned}
$$

关于矩阵的范数，有：
$$
\|A\|_F\triangleq\left(\sum_{j=1}^n\sum_{i=1}^m|a_{ij}|^2\right)^{\frac12}=\left[Trace\left(A^TA\right)\right]^{\frac12}=[Trace(AA^{T})]^{\frac{1}{2}}
$$
那么:
$$
\begin{aligned}
||\mathbf{QK}^{T}-\mathbf{QSK}^{T}||_{F}^{2}= ||\sum_{i\in B}\mathbf{q}_{i}\mathbf{k}_{i}^{T}||^{2} \\
=Trace\left( \left( \sum_{i\in B}\mathbf{q}_{i}\mathbf{k}_{i}^{T} \right)\left( \sum_{i\in B}\mathbf{q}_{i}\mathbf{k}_{i}^{T} \right)^{T} \right)\\
=Trace\left( \sum _{i\in B} \sum_{j \in B} \mathbf{q}_{i}\mathbf{k}_{i}^{T} \mathbf{k}_{j}\mathbf{q}_{j}^{T}\right) \\
=\sum_{i\in B} \sum_{j\in B}Trace(\mathbf{q}_{i}\mathbf{k}_{i}^{T} \mathbf{k}_{j}\mathbf{q}_{j}^{T}) \\
=\sum_{i\in B} \sum_{j\in B} \mathbf{k}_{i}^{T} \mathbf{k}_{j}Trace(\mathbf{q_{i}}\mathbf{q}_{j}^{T}) \\
=\sum_{i\in B} \sum_{j\in B} \mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j}

\end{aligned}
$$

考虑 $B$ 集合中的某个 $i$，它带来的损失是：
$$
\begin{aligned}
\sum_{j\in B}\mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j} \\
=\sum_{j\in B-i}\mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j} + \mathbf{k}_{i}^{T} \mathbf{k}_{i} \times \mathbf{q}_{i}^{T}\mathbf{q}_{i} \\
=\sum_{j\in B-i}\mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j} + Trace(\mathbf{q}_{i}\mathbf{k}_{i}^{T} \mathbf{k}_{i}\mathbf{q}_{i}^{T}) \\
= \sum_{j\in B-i}\mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j} + ||\mathbf{q}_{i}\mathbf{k}_{i}^{T}||_{F}^{2}
\end{aligned}
$$
这个第二项其实就是 Think 中考虑的 score, 但实际上，还有前一项这个交互的损失。

对于任意一个维度 $i$, 把它看成一个节点， $\mathbf{k}_{i}^{T} \mathbf{k}_{i} \times \mathbf{q}_{i}^{T}\mathbf{q}_{i}$ 即为点自身的权重，它与另一个节点 $j$ 之间有一条边相连，边的权重是 $\mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j}$。

于是这个问题等价成了，如何在一个包含 $D$ 个节点的完全图中，找出 $|B|$ 个节点，使得该子完全图的权重和最小。
初始化： $remove$ 集合为空，把这 $D$ 个点自身的权重排序，最小的权重的点加入 $remove$ 集合。
一般化：$remove$ 集合非空，对点 $i, i\not\in remove$，计算其自身权重以及与 $remove$ 集合中点的边权之和，取最小的加入 $remove$ 集合。

上面的算法存在很强的局部性，后面点的加入大大依赖于集合中已有的点，假设第一个加入的点，在全局来看不是优的，会对后面点的加入造成影响。

可以把这个问题反过来看，有 $D$ 个点的完全图，它的权重和是固定的。从这个完全图中抽出一些点，使得余下的点权重和最小，也就是说抽走的点权重得最大。那么，设定集合 $remain$，表示从这个完全图中抽走的点，也就是留下来的那些维度。集合 $C=A\cup B-remain$, 表示完全图中剩下的那些点。

对点 $i\in C$，计算其自身权重以及与 $C$ 中其它点的边权和，把最大的点加入 $remain$。
这样的方法相比前面的方法，更有利于克服局部性的问题。


## softmax
从 $softmax(\mathbf{QK}^{T})$ 角度来看这个问题。
对于一个行向量 $\mathbf{v}\in \mathbb{R}^{1\times N}$，为它的每一个元素加一个常数 $t$，那么
$$
\begin{aligned}
softmax(\mathbf{v}+t)_{i} = \frac{e^{v_{i}+t}}{\sum_{i=1}^{N}e^{v_{i}+t}} \\
=\frac{e^{v_{i}}}{\sum_{i=1}^{N}e^{v_{i}}} \\
= softmax(\mathbf{v})_{i}
\end{aligned}
$$
由前面的推导，有：
$$
\begin{aligned}
softmax(\mathbf{QK}^{T})=softmax\left( \sum _{i\in A \cup B}\mathbf{q}_{i}\mathbf{k}_{i}^{T} \right)
\end{aligned}
$$
对某个 $i$, 如果 $\mathbf{q}_{i}\mathbf{k_{i}^{T}}$ 的每一行的元素都相等，那么有：
$$
softmax(\mathbf{QK}^{T})=softmax\left( \sum _{i\in A \cup B}\mathbf{q}_{i}\mathbf{k}_{i}^{T} \right) = softmax\left( \sum _{i\in A \cup B -i}\mathbf{q}_{i}\mathbf{k}_{i}^{T} \right)
$$
也就是说，$\mathbf{q}_{i}\mathbf{k}_{i}^{T}$ 的范数并不是一个重要的度量，不管你的范数有多大，只要每一行的元素值一样，那就没有任何作用。
能否设计一种衡量这种“差异”的 score 来进行筛选?

但这样的设计依旧有问题，因为最近 token的 K 是不会被压缩的，这样的方法会降低被压缩 token 得到的 attention 分数。而按照范数的方法，这样的影响更小。