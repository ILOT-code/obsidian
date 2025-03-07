
# Image compression

## Implicit Neural Representations for Image Compression
ECCV 2022
![[Pasted image 20250307184031.png]]
## TINC : Tree-structured Implicit Neural Compression
CVPR 2023
树状结构的 INR 来压缩 3D 图像
![[Pasted image 20231016205353.png]]
## COOL-CHIC: Coordinate-based Low Complexity Hierarchical Image Codec
ICCV 2023
对 grid 进行R-D 优化
![[Pasted image 20240107140632.png]]

## C3: High-Performance and Low-Complexity Neural Compression from a Single Image or Video
CVPR 2023
和 cool-chic 一个系列，引入了自回归的熵模型以及其它各种改进。
![[Pasted image 20240711000532.png]]
# Video compression
## E-NeRV: Expedite Neural Video Representation with Disentangled Spatial-Temporal Context
ECCV 2022
![[Pasted image 20250307182127.png]]
## NeRV: Neural Representations for Videos
NIPS 2021
比较早做这方面的文章
![[Pasted image 20250307180934.png]]
## Video Compression With Entropy-Constrained Neural Representations
CVPR 2023
抛弃 grid, 设计更紧凑的网络结构，对网络参数进行熵估计。
![[Pasted image 20240906111014.png]]

## HiNeRV: Video Compression with Hierarchical Encoding-based Neural Representation
NIPS 2023
更紧凑的 INR 表达方法
![[Pasted image 20240904162739.png]]
## Immersive Video Compression using Implicit Neural Representations
PCS 2024
多视角视频压缩
![[Pasted image 20240904194217.png]]

##  Efficient Dynamic-NeRF Based Volumetric Video Coding with Rate Distortion Optimization
VCIP 2024
对帧与帧之间的差异建立一个 $R_{t}$ 网格
![[Pasted image 20240905162319.png]]

## FFNeRV: Flow-Guided Frame-Wise Neural Representations for Videos
[MM '23: Proceedings of the 31st ACM International Conference on Multimedia](https://dl.acm.org/doi/proceedings/10.1145/3581783)
把光流信息融入 INR 中去
![[Pasted image 20240904145059.png]]
## AN EXPLORATION WITH ENTROPY CONSTRAINED 3 D GAUSSIANS FOR 2 D VIDEO COMPRESSION
ICLR 2025
3dgs 来进行视频压缩
![[Pasted image 20241018130448.png]]
## Combining Frame and GOP Embeddings for Neural Video Representation
CVPR 2024
![[Pasted image 20250307181720.png]]
## VQNeRV: Vector Quantization Neural  Representation for Video Compression
ISCAS 2024
引入 VQ
![[Pasted image 20250307181952.png]]

# NeRF 技术
## Instant neural graphics primitives with a multiresolution hash encoding
TOG 2022
NERF 的原始论文
![[Pasted image 20231210114113.png]]

## Compressing Explicit Voxel Grid Representations: Fast NeRFs Become Also Small
WACV 2023
一种剪枝算法，减少 grid 的存储。
![[Pasted image 20240124193418.png]]
## Factor Fields: A Unified Framework for Neural Fields and Beyond
NerF 的改进，对网格的设置和坐标到网格映射的方法作出了改进
![[Pasted image 20240905142141.png]]
## SHACIRA: Scalable HAsh-grid Compression for Implicit Neural Representations
ICCV 2023
一种降维的方法，减少 grid 的大小。
![[Pasted image 20231210115018.png]]

## COINR: COMPRESSED IMPLICIT NEURAL REPRESENTATIONS
压缩 INR 模型权重的方法。
假设某个隐藏层有 $k_{1}$ 个神经元，那么该层的一个权重向量可以写成 $\mathbf{w}\in \mathbb{R}^{k_{1}}$。它们可以看成一个个符合正态分布的随机变量。
![[Pasted image 20241112144950.png]]

因此，可以用足够多的随机变量的线性表达，来表示该权重向量，即 $\mathbf{w}=\mathbf{A}\mathbf{x},\mathbf{A}\in \mathbb{R}^{k_{1}\times k_{2}},\mathbf{x}\in \mathbb{R}^{k_{2}}$ 
$k_{2}$ 当然得足够大。我们并不需要来存储 $\mathbf{A}$，只需要固定发送端和接收端的随机数种子，然后以相同的顺序来生成字典 $\mathbf{A}$ 即可。
假设 $\mathbf{x}$ 中非 0 元素的数量是 $s$，那么就需要 $2s$ 个数去存储 $x$。只需要限定 $2s<k_{1}$，就能达到压缩效果。
# 3dgs 技术
## HAC: Hash-grid Assisted Context for 3D Gaussian Splatting Compression
ECCV 2024
引入了熵模型来减少 3dgs 的参数。
![[Pasted image 20241018125808.png]]

## Scaffold-GS: Structured 3 D Gaussians for View-Adaptive Rendering
CVPR 2024
对 3DGS 渲染技术的改进。用少量高斯锚点来生成大量高斯基元。
![[Pasted image 20250307174935.png]]