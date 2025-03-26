# TreeKV: Smooth Key-Value Cache Compression with Tree Structures

## Introduction
KV-cache 随着 generation step 的增长，线性增长，若无法摆脱线性增长的机制，就难以面对长序列生成。本文设计了一种 KV-cache 的淘汰策略，它的 KV-cache size 是固定的。

现有的相关研究，大多基于全局的注意力分数来进行淘汰。这种基于全局选择的机制存在着明显的区域偏好，也就是侧重于保存某些位置的 kv-cache.

本文先使用小波分解来分析 kv-cache，基于发现的规律提出了 TreeKV，他的 kv-cacheuj 保留结果更平滑。