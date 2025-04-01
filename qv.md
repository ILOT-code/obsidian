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
=\sum_{i\in B} \sum_{j\in B}Trace()


\end{aligned}
$$