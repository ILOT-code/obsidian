## think 流程
设 prompt 的长度是 $l_{1}$，那么模型第一次 generate 会得到 $Q,K,V\in R^{l_{1}\times d}$。
首先对使用其他的 kv cache 淘汰策略，如 snapkv, h2o, 来保留 $l_{2}<l_{1}$ 个 kv cache, 得到 $K_{prompt}, V_{prompt} \in R^{l_{2}\times d}$。

think 的思路是，对 $K_{prompt}$ 的通道进行削减，但它不会对全部 $l_{2}$ 个 key 都进行削减，会保留最后的 $recent$ 个 key 不进行削减。最后就得到两部分：$K_{prun}\in R^{l_{2}-recent \times d'}, K_{noprun}\in R^{recent\times d}$。

在生成过程中，新生成的 token, 其对应的 key, 同样是不会被削减的，被加入到 $K_{noprun}$ 中。假设已经生成了 $t$ 个 token, 那么 $K_{noprun}\in R^{recent+t \times d}$.

新来的 token $q\in R^{1\times d}$ ，其对应的稀疏版本写作 $q'\in R^{1\times d'}$, 计算 attention 的方式是：
$$
softmax(concat(q'K_{prun}^{T},qK_{noprun}^{T}))  --1
$$

在处理 prompt 的过程中，think 这样来选择 $d'$，下面的的 $Q,K \in R^{l_{1}\times d}$
![[Pasted image 20250414164519.png]]

这个优化方程其实是有问题的，从式 1可以看出来，需要近似的并不是 $QK^{T}$ 矩阵，而应该是 $QK[l_{2}-recent,:]^{T}$.

调整优化方程之后(称为 align)，我们的方法有了进步：

prun_ratio 50%：

| method         | avg_score |
| -------------- | --------- |
| global_align   | 40.40     |
| local_align    | 40.50     |
| think_align    | 40.30     |
| think_noalign  | 40.30     |
| local_noalign  | 40.43     |
| glocal_noalign | 40.36     |

prun_ratio 60%:

| method       | avg_score |
| ------------ | --------- |
| glocal_align | 39.31     |
| local_align  | -1        |
| think_align  | 39.08     |
| think_no     | 39.18     |
| local_no     | 39.03     |
| global_no    | 38.96     |
local_align 由于显存不够无法计算。

在对齐之后，我们的方法有了提升，大概提升了 0.2～0.3。
这个幅度其实还凑合，上交华为的那篇 Tree KV 的提升幅度更加有限：