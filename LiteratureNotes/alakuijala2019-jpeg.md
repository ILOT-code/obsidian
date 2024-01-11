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

AFV transfoem：这个和前两个类似，不同的是，对于每个 4\*4 的子区域，采用不同的 transformer


对于其他的块比较大的情况，比如 8\*16 16\*16，有一个问题是，对于这些大的区域，只有一个直流分量。为此，对于 $X \times Y$ 的区域，会计算 $\frac{X}{8}\times \frac{Y}{8}$ 个“伪直流分量”，它们大致等于那个子 8\*8 区域的平均值。

### Entropy coding

JPEG-XL 使用 [[ANS及其实现技巧|ANS]] 来进行编码。
JPEG XL 在单个 ANS 流中对从多个不同概率分布中提取的符号进行编码。JPEG-XL 使用两种方法来提高压缩效率：
1. 把相似的分布聚类，为整个簇编码单个直方图。
2. 可变精度编码：为了减少不同符号的数量，JPEG-XL 使用这样的整数编码策略：values below 16 are encoded as symbols directly, while any other value is encoded with a symbol that allows to recover the highest 2 bits set; other bits are stored uncompressed.


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
JPEG 使用 zig-zag 的方式来编码 AC，以期望捕获更多的连续 0。在 JPEG-XL 中，对于不同大小的块，它将 zig-zag 泛化到适用这些块的情况。另外，对于特定的图片，固定的顺序并不一定最好，因此实际上使用的是 zig-zag 顺序的一个排列，使用一种编码：Lehmer-like code 去高效地编码该排列。

在编码 AC 的过程中，会记录下当前这个块中剩余的非零值数量，这个数量和当前位置一起，构成了对其概率分布的描述。

### Loop filters
JPEG-XL 使用两种不同的环路滤波器来减少 ringing 伪影（在解码之后进行）。

第一个是平滑卷积。由于平滑卷积会增大图像质量损失，作为补偿，在进行 DCT 之前，使用了一个锐化空间滤波器。此过程的总体效果是减少了块边界的视觉影响，同时仍然保留了原始图像中存在的锐利细节。

第二个滤波器旨在减少 ringing 伪影，同时保持图像的纹理细节，它应用了与非局部均值相关的自适应平滑算法以达到这个目的。

### Group splitting

如果要编码的图像很大，则将其分割为大小最大为 256 × 256 的子矩形，并独立编码。
这带来了一些好处：
1. 它允许解码器并行独立地处理每个矩形
2. 仅解码大图像的选定区域
3. 按照某种顺序重新排列矩形，比如重要性顺序