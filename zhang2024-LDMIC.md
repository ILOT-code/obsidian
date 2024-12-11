---
zotero-key: IW 2 FDBIC
zt-attachments:
  - "145"
title: "CLDMIC: LEARNING-BASED DISTRIBUTED MULTIVIEW IMAGE CODING"
authors: Xinjie Zhang
doi: 
conference: ICLR2023
citekey: 
tags:
---
# LDMIC: LEARNING-BASED DISTRIBUTED MULTIVIEW IMAGE CODING


> [!abstract]
> 在多视角视频压缩方面，现有压缩器多采用预测编码的模式，这需要联合所有相机同时进行编码。并且，这些方法往往对相机的排列布置具有严格的要求，这使得在具有随机重叠视场的分布式相机系统中部署这些方法变得具有挑战性。
> distributed source coding theory 指出，分布式编码与联合解码的方式可以达到很好的数据压缩效果，基于此，本文就设计了这样的编码架构，独立编码，并在解码端引入注意力机制来构建视角间的联系。


现有的各种 MIC 具有以下问题：
1. 编码时需要进行通信
2. 大多数主流方案都是基于对极几何约束定义的视差相关性来开发的（Scharstein & Szeliski，2002），这通常需要提前知道相机的内部和外部参数，例如相机位置、方向和相机矩阵。然而，没有通信的分布式相机系统很难访问相机的先验知识（Devarajan et al., 2008）。例如，自动驾驶中摄像头的具体位置信息通常不希望被其他车辆或基础设施感知，以避免泄露个人的位置和轨迹（Xiong et al., 2020）







