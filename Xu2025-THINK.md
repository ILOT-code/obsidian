---
zotero-key: 4XIGHJ9W
zt-attachments:
  - "75"
title: "THINK: THINNER KEY CACHE BY QUERY-DRIVEN  PRUNING"
authors:
  - Yuhui Xu
doi: 10.48550/arXiv.2211.06689
conference: ICLR2025
citekey: 
tags:
---
# THINK: THINNER KEY CACHE BY QUERY-DRIVEN  PRUNING
本文也致力于kv-cache的缩减，和之前方法不同的是，它是从channel的维度进行缩减的，因此，他和其他在时间维度上进行缩减的方法正交。

**为什么对channel进行削减**
在不同的层上，key的通道间，数据的分布都极不平衡，少数几个通道占据了很大的值，这意味着
![[Pasted image 20250326151951.png]]