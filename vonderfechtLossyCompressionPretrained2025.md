---
zotero-key: RFVNJ 6 JH
zt-attachments:
  - "306"
title: LOSSY COMPRESSION WITH  PRETRAINED DIFFUSION MODELS
authors:
  - Jeremy Vonderfecht
conference: ICLR 2025
citekey: 
tags:
---
# reverse channel coding
如果用概率分布 $p$ 去编码真实分布 $q$ 中的数据，需要的编码长度是它们的交叉熵。这样的编码是我们熟知的熵编码，他可以用 ans, ac 等技术来实现。这种熵编码是“正向的”，也就是先有数据，再有一个对数据分布的估计，然后去进行编解码操作。

而 reverse channel coding 的流程和上面不同，他是先有一个分布（编解码端都能访问到），