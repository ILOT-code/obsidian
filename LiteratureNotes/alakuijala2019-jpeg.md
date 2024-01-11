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

使用两种技术为编码器提供更大的灵活性：分布聚类和可变精度分布编码。
