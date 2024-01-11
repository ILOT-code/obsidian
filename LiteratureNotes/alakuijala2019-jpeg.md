---
zotero-key: BK8HIDUN
zt-attachments:
  - "396"
title: JPEG XL next-generation image compression architecture and coding tools
authors:
  - Jyrki Alakuijala
  - Ruud van Asseldonk
  - Sami Boukortt
  - Martin Bruse
  - Iulia-Maria Comșa
  - Moritz Firsching
  - Thomas Fischbacher
  - Evgenii Kliuchnikov
  - Sebastian Gomez
  - Robert Obryk
  - Krzysztof Potempa
  - Alexander Rhatushnyak
  - Jon Sneyers
  - Zoltan Szabadka
  - Lode Vandevenne‎
  - Luca Versari
  - Jan Wassenberg
doi: 10.1117/12.2529237
conference: xxx
citekey: alakuijala2019-jpeg
tags: []
---
# JPEG XL next-generation image compression architecture and coding tools

**文章链接**: [Zotero](zotero://select/library/items/BK8HIDUN) 
**网页链接**: [URL](https://www.spiedigitallibrary.org/conference-proceedings-of-spie/11137/111370K/JPEG-XL-next-generation-image-compression-architecture-and-coding-tools/10.1117/12.2529237.full)


## CODEC ARCHITECTURE
### Integral transforms
JPEG-XL 支持可变的 DCT 变换，不同于 JPEG 的固定 8\*8.

小的变换：8\*4 4\*4 2\*2 4\*2被用在很"不均匀"的区域里。但小的块会加大伪影的影响，解决方法是：一切小的变换是在 8\*8 这个区域上进行的。比如说对于 4\*4 的块，会在 8\*8 的区域上的四个角落进行 4\*4 的 DCT，然后，4 个直流部分会被放置在左上的 2\*2 里，然后对这个 2\*2 的区域再进行一次 DCT，最后得到一个直流分量。


IDENTITY transform：这被应用在块中某些像素和周围像素有非常不同的值的情况。和上面的小的变换类似，对于 4 \*4 的块，同样是在 8\*8 的区域上进行。但不同点是，每个子区域并不是使用 DCT 变换：它的直流部分是计算平均值，然后其它部分是计算和 (1,1)位置的差。之所以不取（0，0）位置是因为作者认为 (1,1)能更好的表示这个块的情况。

AFV transfoem：


对于其他的块比较大的情况，比如 8\*16 16\*16，有一个问题是，对于这些大的区域，只有一个直流分量。为此，对于 $X \times Y$ 的区域，会计算 $\frac{X}{8}\times \frac{Y}{8}$ 个“伪直流分量”，它们大致等于那个子 8\*8 区域的平均值。

### Entropy coding

JPEG-XL 使用 [[ANS及其实现技巧|ANS]] 来进行编码。
JPEG XL 在单个 ANS 流中对从多个不同概率分布中提取的符号进行编码。JPEG-XL 使用两种方法来提高压缩效率：
1. 把相似的分布聚类，
2. 可变精度编码：为了减少不同符号的数量，JPEG-XL 使用这样的整数编码策略：


### Color correlation

假设有一张灰度图，转化为 XYB 空间后，其表示一定是（0, 1, 0.935669）这个三元组的倍数。作者认为这并不好，因为原本单独的亮度信息出现在了 Y，B 两个通道上。因此，实际上，仍然只使用 Y 这一个通道传递亮度，再反量化后，将 Y 通道的值分别乘以 0 和 0.935669 放到 X，B 通道。

对于有色度的图像，这样并不一定最优，因此，JPEG-XL 设置了专门的控制字段来设定这些相关系数。

### Adaptive quantization

在 JPEG XL 中，量化矩阵的选择是全局的，然而，这个量化矩阵可以局部缩放，以减少更“复杂”区域中的伪影，而不增加图像其他部分使用的位数。

### Adaptive predictor

在编码直流系数时，JPEG 会将当前 DC 减去先前的 DC 来编码。这并未考虑到图像的二维模式，在 JPEG-XL 中，会自适应选择 8 种预测器。它们被用来编码 DC 值和各种控制字段。
在编码 (x, y)的值时，会看各种预测模式在 (x-1, y), (x-1, y-1), (x, y-1)处产生的误差，并选择最少的那一个。这个误差以及所选的预测模式，被用来区分残差的概率分布。

在无损编码中，预测器类似但数量更少：4，并且预测时，并不会选择某一个预测器，而是它们的加权平均。

### DC handling

在颜色变化很平缓的区域，较低质量的 JPEG 会出现严重的伪影--条带。这是由于对 DC 的量化引起的。在 JPEG-XL 中，对残差接近 0 的那些值采用更加精细的量化。

### AC encoding

