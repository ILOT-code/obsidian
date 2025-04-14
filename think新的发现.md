## think 流程
设 prompt 的长度是 $l_{1}$，那么模型第一次 generate 会得到 $Q,K,V\in R^{l_{1}\times d}$。
首先对使用其他的 kv cache 淘汰策略，如 snapkv, h2o, 来保留 $l_{2}<l_{1}$ 个 kv cache, 得到 $K_{prompt}, V_{prompt} \in R^{l_{2}\times d}$。

think 的思路是，对 $K_{prompt}$ 的通道进行削减，但它不会对全部 $l_{2}$ 个 key 都进行削减，会保留最后的 $recent$ 个 key 不进行削减。最后就得到两部分：$k_{prun}\in R^{l_{2}-recent \times d'}, K_{noprun}\in R^{recent\times d}$。

在生成过程中，新生成的 token, 其对应的 key, 同样是不会被削减的，被加入到 $K_{noprun}$ 中。假设已经生成了 $t$ 个 token, 那么 $K_{noprun}\in R$
