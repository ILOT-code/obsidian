考虑矩阵 $\mathbf{Q}, \mathbf{K} \in \mathbb{R}^{N\times D}$，如何选取 $\mathbf{Q,K}$ 的某些维度，而抛弃剩下的维度，使得 $\mathbf{QK^{T}}$ 的计算结果损失最小。即下面的优化问题：
$$
\begin{aligned}
\min_{\mathbf{S}}&\left\|\mathbf{Q}\mathbf{K}^T-\mathbf{Q}\mathbf{S}\mathbf{K}^T\right\|_F \\
\text{subject to}&~\operatorname{Trace}(\mathbf{S})=\lfloor(1-\lambda)D\rfloor\\&\mathbf{S}=\operatorname{diag}(s_1,s_2,\ldots,s_D),\text{ where }s_j\in\{0,1\}
\end{aligned}
$$
用 $\mathbf{q}_{i},\mathbf{}$