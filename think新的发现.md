## think 流程
设 prompt 的长度是 $l_{1}$，那么模型第一次 generate 会得到 $Q,K,V\in R^{l_{1}\times d}$。
首先对使用其他的 kv cache 淘汰策略，如 snapkv, h2o, 来保留 $l_{2}<l_{1}$ 个 kv cache, 得到 $K_{prompt}, V_{prompt} \in R^{l_{2}\times d}$。

think 的思路是，对 $K_{prompt}$ 的通道进行削减，但它不会