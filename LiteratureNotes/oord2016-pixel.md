---
zotero-key: L4HL5WST
zt-attachments:
  - "264"
title: Pixel Recurrent Neural Networks
authors:
  - Aaron van den Oord
  - Nal Kalchbrenner
  - Koray Kavukcuoglu
doi: 10.48550/arXiv.1601.06759
conference: xxx
citekey: oord2016-pixel
tags: []
---
# Pixel Recurrent Neural Networks

**文章链接**: [Zotero](zotero://select/library/items/L4HL5WST) [attachment](<file:///home/ilot/Zotero/storage/54GFPZC9/Oord%20%E7%AD%89%20-%202016%20-%20Pixel%20Recurrent%20Neural%20Networks.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/1601.06759)
## Abstract

>[!abstract]
>Modeling the distribution of natural images is a landmark problem in unsupervised learning. This task requires an image model that is at once expressive, tractable and scalable. We present a deep neural network that sequentially predicts the pixels in an image along the two spatial dimensions. Our method models the discrete probability of the raw pixel values and encodes the complete set of dependencies in the image. Architectural novelties include fast two-dimensional recurrent layers and an effective use of residual connections in deep recurrent networks. We achieve log-likelihood scores on natural images that are considerably better than the previous state of the art. Our main results also provide benchmarks on the diverse ImageNet dataset. Samples generated from the model appear crisp, varied and globally coherent.

## Comments
双向对角线 rnn 设计。深蓝色是正向过程中参考点，浅蓝色是逆向过程参考点。和 CNN 不同的是，当前点的感受野是其左和其上的所有点，而不是像 CNN 那样感受野随着卷积层的增加而增加。这个特性是 LSTM 的 h 和 s 随着时间步而传递的性质而来的。


![[Pasted image 20240902162523.png]]
在 LSTM之前，先进行了 conv 处理。这样该值就并不是预测下一对角线上的值，而是本身位置的值
![[Pasted image 20240902162823.png]]

> [!note] Page 4
> 
> the right output map is then shifted down by one row and added to the left output map.
> 
> ---
> 🔤然后右输出映射向下移动一行并添加到左输出映射中。🔤
> ^7AMZDDG3a54GFPZC9p4

