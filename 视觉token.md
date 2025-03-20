
系统提示词、图像、用户问题、已生成的回答这些 token 被一股脑丢给 LLM 来处理。在 LLaVA 模型中，图像 token 的占比非常高。直观的感受是，图像 token 的数量肯定是冗余的，人类在看一张图片时，只会根据问题来专注某一小部分区域，或是专注于图像的某个特点。对视觉 token 的压缩有很大潜力。
下面我总结了一些论文中的方法以及他们发现的现象。

## attention 的不平衡

测试在解码输出的 token 过程中，各类型的 token 获得的 attention 之和以及效率（也就是 attention 的和除以该 token 的数量）。
![[Pasted image 20250320140603.png]]
image tokens 数量占绝大部分。在浅层中，attention 的分布还较为均衡，而在 deep layerh 中，分布局部均衡，image token 的效率也极低，attention 几乎都聚集在 sys tokenh 上。
(这种现象我认为并不能完全说明 image token 的冗余，因为 deep layer 的输入的 token 序列本来就是 shallow layer 处理来的，而 shallow layer 中 image token 占据了很大一部分 attention)

下面的图展示了在逐 tokenh 输出的过程中，各类型 token 的 attention 占比。说明随着输出序列的增加，img token 的 attention 占比也在减少。
![[Pasted image 20250320144315.png]]

## FastV
在第 $K$ 层，对 token 的重要性进行排序，并丢弃最后的 $R\%$ 的token。（这些被丢弃的 token 在随后的层中也不存在）
![[Pasted image 20250320143327.png]] 
## DyRate
generation 过程中，image token 的 attention 占比逐渐减少，因此，需要设计一种动态的 token 削减策略，在不同的 generation steps以不同的比例来剪去 image token.
![[Pasted image 20250320152556.png]]
![[Pasted image 20250320152654.png]]