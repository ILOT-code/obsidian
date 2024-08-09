---
zotero-key: KTV6F2E9
zt-attachments:
  - "134"
title: Modulated Periodic Activations for Generalizable Local Functional Representations
authors:
  - Ishit Mehta
  - Michaël Gharbi
  - Connelly Barnes
  - Eli Shechtman
  - Ravi Ramamoorthi
  - Manmohan Chandraker
doi: 10.48550/arXiv.2104.03960
conference: xxx
citekey: mehta2021-modulated
tags: []
---
# Modulated Periodic Activations for Generalizable Local Functional Representations

**文章链接**: [Zotero](zotero://select/library/items/KTV6F2E9) [attachment](<file:///home/ilot/Zotero/storage/RDKFP4PN/Mehta%20%E7%AD%89%20-%202021%20-%20Modulated%20Periodic%20Activations%20for%20Generalizable%20L.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2104.03960)
## Abstract

>[!abstract]
>Multi-Layer Perceptrons (MLPs) make powerful functional representations for sampling and reconstruction problems involving low-dimensional signals like images,shapes and light fields. Recent works have significantly improved their ability to represent high-frequency content by using periodic activations or positional encodings. This often came at the expense of generalization: modern methods are typically optimized for a single signal. We present a new representation that generalizes to multiple instances and achieves state-of-the-art fidelity. We use a dual-MLP architecture to encode the signals. A synthesis network creates a functional mapping from a low-dimensional input (e.g. pixel-position) to the output domain (e.g. RGB color). A modulation network maps a latent code corresponding to the target signal to parameters that modulate the periodic activations of the synthesis network. We also propose a local-functional representation which enables generalization. The signal's domain is partitioned into a regular grid,with each tile represented by a latent code. At test time, the signal is encoded with high-fidelity by inferring (or directly optimizing) the latent code-book. Our approach produces generalizable functional representations of images, videos and shapes, and achieves higher reconstruction quality than prior works that are optimized for a single signal.

## Comments

