
# Image compression
## TINC : Tree-structured Implicit Neural Compression
树状结构的 INR 来压缩 3D 图像
![[Pasted image 20231016205353.png]]
## COOL-CHIC: Coordinate-based Low Complexity Hierarchical Image Codec
对 grid 进行R-D 优化
![[Pasted image 20240107140632.png]]

## C3: High-Performance and Low-Complexity Neural Compression from a Single Image or Video
和 cool-chic 一个系列，引入了自回归的熵模型以及其它各种改进。
![[Pasted image 20240711000532.png]]
# Video compression
## NeRV: Neural Representations for Videos

## Video Compression With Entropy-Constrained Neural Representations
抛弃 grid, 设计更紧凑的网络结构，对网络参数进行熵估计。
![[Pasted image 20240906111014.png]]

## HiNeRV: Video Compression with Hierarchical Encoding-based Neural Representation
更紧凑的 INR 表达方法
![[Pasted image 20240904162739.png]]
## Immersive Video Compression using Implicit Neural Representations
多视角视频压缩
![[Pasted image 20240904194217.png]]

##  Efficient Dynamic-NeRF Based Volumetric Video Coding with Rate Distortion Optimization
对帧与帧之间的差异建立一个 $R_{t}$ 网格
![[Pasted image 20240905162319.png]]

## FFNeRV: Flow-Guided Frame-Wise Neural Representations for Videos
把光流信息融入 INR 中去
![[Pasted image 20240904145059.png]]
# NeRF 技术
## Instant neural graphics primitives with a multiresolution hash encoding
NERF 的原始论文
![[Pasted image 20231210114113.png]]

## Compressing Explicit Voxel Grid Representations: Fast NeRFs Become Also Small
## Factor Fields: A Unified Framework for Neural Fields and Beyond

## SHACIRA: Scalable HAsh-grid Compression for Implicit Neural Representations

# 3dgs 技术
## HAC: Hash-grid Assisted Context for 3D Gaussian Splatting Compression

## AN EXPLORATION WITH ENTROPY CONSTRAINED 3 D GAUSSIANS FOR 2 D VIDEO COMPRESSION

## Scaffold-GS: Structured 3 D Gaussians for View-Adaptive Rendering
对 3DGS 渲染技术的改进。用少量高斯锚点来生成大量高斯基元。
![[Pasted image 20250307174935.png]]