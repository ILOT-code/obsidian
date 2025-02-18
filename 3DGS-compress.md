# 整体性压缩
这类压缩方法不局限于减少高斯基元某种属性的存储，而是从整体上来减少数据的存储。
## 紧凑性表达
![[Pasted image 20250218154655.png]]
高斯一般被描绘成“点云”的结构，具有如上59个属性。这种看法具有割裂性，空间位置上接近的高斯点大概率具有强相关性。挖掘这种相关性能从整体层面减少数据冗余。
### Scaffold-gs: Structured 3d gaussians for view-adaptive rendering
很多研究都表明，3DGS训练过程的“致密化”操作生成了大量冗余的高斯基元。Scaffold-gs直接舍弃了“致密化”操作，整个scene由少量锚点及锚点上的“属性”构成。单个锚点通过它的“属性”，在其邻域内会生成 $k$ 个高斯点。这样的表达方式降低了高斯点空间坐标上的冗余。

具体而言，单个锚点具有 3 种可训练的参数：
- $f_{v}\in \mathbb{R}^{32}$：特征向量，经过 MLP来生成高斯点的属性。
- $l_{v}\in \mathbb{R}^{3}$：scaling factor，用于缩放offset
- $\mathbf{}{O}_{v}\in \mathbb{R}^{k\times 3}$：offset, 生成的高斯基元坐标相对于锚点坐标的 offset

$f_v$会被进一步调制成与相机坐标以及锚点坐标相关的量， 以更好的生成“颜色”这个与观察角度有关的属性。

锚点的位置与数量对scene的重建很重要，scaffold-gs设计了锚点的增长机制：
![[Pasted image 20241017105937.png]]

### HAC: Hash-grid Assisted Context for 3 D  Gaussian Splatting Compression
HAC 建立在 scaddold-gs

## 减少高斯基元
## 数据层面压缩

# SH压缩
## 自适应SH等级
