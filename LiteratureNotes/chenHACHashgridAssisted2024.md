---
zotero-key: GHS7ZMTS
zt-attachments:
  - "403"
title: "HAC: Hash-grid Assisted Context for 3D Gaussian Splatting Compression"
authors:
  - Yihang Chen
  - Qianyi Wu
  - Weiyao Lin
  - Mehrtash Harandi
  - Jianfei Cai
doi: 10.48550/arXiv.2403.14530
conference: xxx
citekey: chenHACHashgridAssisted2024
tags: []
---
# HAC: Hash-grid Assisted Context for 3D Gaussian Splatting Compression

**文章链接**: [Zotero](zotero://select/library/items/GHS7ZMTS) [attachment](<file:///home/ilot/Documents/Zotero/storage/FPYYAFK8/Chen%20%E7%AD%89%20-%202024%20-%20HAC%20Hash-grid%20Assisted%20Context%20for%203D%20Gaussian%20Splatting%20Compression.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2403.14530)
## Abstract

>[!abstract]
>3D Gaussian Splatting (3DGS) has emerged as a promising framework for novel view synthesis, boasting rapid rendering speed with high fidelity. However, the substantial Gaussians and their associated attributes necessitate effective compression techniques. Nevertheless, the sparse and unorganized nature of the point cloud of Gaussians (or anchors in our paper) presents challenges for compression. To address this, we make use of the relations between the unorganized anchors and the structured hash grid, leveraging their mutual information for context modeling, and propose a Hash-grid Assisted Context (HAC) framework for highly compact 3DGS representation. Our approach introduces a binary hash grid to establish continuous spatial consistencies, allowing us to unveil the inherent spatial relations of anchors through a carefully designed context model. To facilitate entropy coding, we utilize Gaussian distributions to accurately estimate the probability of each quantized attribute, where an adaptive quantization module is proposed to enable high-precision quantization of these attributes for improved fidelity restoration. Additionally, we incorporate an adaptive masking strategy to eliminate invalid Gaussians and anchors. Importantly, our work is the pioneer to explore context-based compression for 3DGS representation, resulting in a remarkable size reduction of over $75\times$ compared to vanilla 3DGS, while simultaneously improving fidelity, and achieving over $11\times$ size reduction over SOTA 3DGS compression approach Scaffold-GS. Our code is available here: https://github.com/YihangChen-ee/HAC

## Comments

### 创新点

> [!note] Page 6
> 
> we contend there is still significant redundancy among inherent consistencies of anchors that we can fully exploit for a more compact 3DGS representation.
> ^Z5U4NTPVaFPYYAFK8p6

