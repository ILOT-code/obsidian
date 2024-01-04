---
zotero-key: YT84HU2W
zt-attachments:
  - "381"
title: "FLIF: Free lossless image format based on MANIAC compression"
authors:
  - Jon Sneyers
  - Pieter Wuille
doi: 10.1109/ICIP.2016.7532320
conference: xxx
citekey: sneyers2016-flif
tags: []
---
# FLIF: Free lossless image format based on MANIAC compression

**文章链接**: [Zotero](zotero://select/library/items/YT84HU2W) 
**网页链接**: [URL](https://ieeexplore.ieee.org/document/7532320)
## Abstract

>[!abstract]
>We present a novel lossless image compression algorithm. It achieves better compression than popular lossless image formats like PNG and lossless JPEG 2000. Existing image formats have specific strengths and weaknesses: e.g. JPEG works well for photographs, PNG works well for line drawings or images with few distinct colors. For any type of image, our method performs as good or better (on average) than any of the existing image formats for lossless compression. Interlacing is improved compared to PNG, making the format suitable for progressive decoding and responsive web design.

## COLOR TRANSFORMATIONS
**颜色转换**
FLIF uses the reversible YCoCg color transformation of lossless JPEG 2000
$$
\begin{bmatrix}Y\\Co\\Cg\end{bmatrix}=\begin{bmatrix}\frac{1}{4}&\frac{1}{2}&\frac{1}{4}\\\frac{1}{3}&0&-\frac{1}{2}\\-\frac{1}{4}&\frac{1}{2}&-\frac{1}{4}\end{bmatrix}\cdot\begin{bmatrix}R\\G\\B\end{bmatrix}
$$
$$
\begin{bmatrix}R\\G\\B\end{bmatrix}=\begin{bmatrix}1&1&-1\\1&0&1\\1&-1&-1\end{bmatrix}\cdot\begin{bmatrix}Y\\Co\\Cg\end{bmatrix}
$$

**调色板**
FLIF supports arbitrary palette sizes and both 4-channel (YCoCgA) and 3-channel palettes (YCoCg), encoding the alpha channel separately if needed.
**颜色桶**
对于一个固定的 $Y_1$，找到图片中全部的 $Y_1-Co$ 的组合，如果组合数量少，为它们维护一张索引表，一个索引值对应着一个 $Co$ 值。如果组合数过多，为 $Co$ 维护上下界。同样的，对于一个固定的 $Y-Co$ 对，去维护对应 $Cg$ 值的颜色桶。

颜色桶在预测阶段也起了作用：如果某个像素点的颜色分量对应一个离散的桶，把预测值规整为桶里最近的一个值，否则规整到那个区间中去。
```c++
    ColorVal snapColor_slow(const ColorVal c) const {
        if (c <= min) return min;
        if (c >= max) return max;
        if (discrete) {
          ColorVal mindiff = abs(c-min);
          unsigned int best = 0;
          for(unsigned int i=1; i < values.size(); i++) {
                if (c == values[i]) return c;
                ColorVal diff = abs(c-values[i]);
                if (diff < mindiff) {best = i; mindiff = diff;}
                if (values[i] > c) break; // can safely skip the rest, values is sorted
          }
          return values[best];
        }
        return c;
    }
```
## IMAGE TRAVERSAL AND PIXEL PREDICTION
![[Pasted image 20240103191928.png|500]]
两种遍历图片的方式：
**Scanline traversal**：从上到下，从左到右的扫描方式，预测值是 $T,L,T+L-TL$ 的中位数，这和 FFV1 一样。这种扫描方法在颜色变换平滑的图像中很有用。
**Adam∞ Interlacing.**：此扫描方式能够渐进的解码图片。定义了三种预测器：
1. The average of top and bottom (T + B)/2 (horizontal step) or left and right (L + R)/2 (vertical step); 
2. The median of: the above average, the top-left gradient T +L−TL, and the gradient L+B−BL or T +R−TR;
3. The median of 3 known neighbors (L, T , and B or R).

## ENTROPY CODING: MANIAC

MANIAC 是对 FFV1 中的编码引擎 CABAC 的改进。下面首先介绍 CABAC：
[[marpe2003-contextbased|(D. Marpe, 2003)]]

**binarization;**
在 MANIAC 中，使用了类似 FFV1 中的二值化方法：首先，第一个位单独用来表示这个数是否是 0，然后第二个位表示符号；再使用一元码来 $e=\lceil\log(|x|)\rceil$ ,表示尾数的长度，最后输出尾数。作出的改进是将一些冗余的位去掉：
![[Pasted image 20240104131053.png]]

**上下文模型**

上下文模型的数量是很难人工控制的常量，过多与多少都会对预测性能造成影响。在 CABAC 的原始提出论文中，是使用了固定数量的模型数量。在 MANIAC 中，使用了自适应的模型数量，构建一颗自适应生长的决策树树。树的叶子节点代表了一个上下文模型。设立一个判别机制来判断此叶子模型能否适当的预测当前值，如若不能，设立了一种叶子节点的分裂机制，生成两个不同的叶子节点。

上下文模型的选择由交错模式的不同而不同，下面介绍最普通的扫描模式：

决策树上的非叶子节点，是根据某个属性进行判断。在普通的 Scanline traversal 模式中，选择的五个基本属性是：$L − TL, TL− T , T − TR, LL − L, TT − T$，再附加一些属性：预测值、一个表征预测值来源的数（预测器有好几种）、之前的通道上同一像素位置的值。

**树结构**
每个通道都有对应的决策树，下面是 Y 通道的一个例子。
![[Pasted image 20240104132616.png]]
非根节点上是对属性的判断，从根节点到叶子节点的路径表征了一条判断链。
叶子节点上，包含了一个“真正的”概率表，并且，对于每一个属性，其值是该模型在这个属性上的平均值。每个属性包含了两个“虚拟的”概率表，分别对应小于该值或大于该值时应用的概率表。

编码一个值时，从树根进行决策，直到到达一个叶子；然后使用“真正的”概率表去得到输出以及预估的压缩位数。再对每个属性，选择对应的那个“虚拟”概率表，得到预估的压缩位数，同时更新概率表，

**分裂**
对于某个叶子节点，如果一个属性在该上下文中是无关的，那么它对应的两个虚拟概率表得到的预估位数，应该会大于等于真正概率表的结果。反之，如果某个属性，选择对应的虚拟概率表后，其预估结果更好，说明该属性是相关的，并且有进一步分解的价值。找出最好的一个属性，如果其结果与真正的概率表的结果的差值超过阈值，就将此叶子节点以该属性进行分解：
![[Pasted image 20240104150700.png]]

MANIAC 带来了很多好处：
1. 区分上下文模型的属性并不需要量化。这意味着有更高的区分精度，能够区分相近却又不同的属性。
2. 上下文数量自适应变化，复杂的图片产生更多上下文，反之亦然。
3. 属性只有在它们真正起作用时才被使用，避免了盲目的选择确定的属性来区分上下文模型。