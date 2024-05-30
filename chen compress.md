
### Significantly improving lossy compression for scientific data sets based on multidimensional prediction and error-controlled quantization
2017 IEEE International Parallel and Distributed Processing Symposium (IPDPS)
引用数：287

对大数据进行的有损压缩，大数据指科学模拟计算过程中产生的庞大的、高维的数据。
本文提出了一种预测模型，采用 point by point 的方法，根据已经预测出的点来预测下一个点。
![[Pasted image 20240530151345.png]]
这张图展示的是一个二维数据的例子，根据那些打叉的点 (已经处理完毕) 来预测下一个点。其中，"layer"即为向前参考的层数，根据层数的不同，预测的函数式也不同：
![[Pasted image 20240530151635.png]]
另外，是他们提出的控制量化误差的方法。用户提供一个 error_bound，限定允许的最大误差，他们使用下面的方法来满足这个限定要求：
![[Pasted image 20240530152711.png]]
红色的 First-phase Predicted Value 就是指使用预测模型得到的预测值，以 2\*Error_bound 为间隔，以红色点为中心，扩展成 $2^m-2$ 个值。这些值之间构成了 $2^m$ 的区间，用 m 个 bit 就可以编码它们，如果真值落在了某个区间内，他就是可预测的，编码这个区间即可。反之，采用其它方法来编码。这样就能满足误差不超过 error_bound.

### Error -Controlled Lossy Compression Optimized for High Compression Ratios of Scientific Datasets
2018 IEEE International Conference on Big Data (Big Data)
引用数：233

也是对科学计算产生的数据进行压缩压缩。把数据集分成不同的区域，根据各个区域的特征自适应选择合适的预测方法。他们根据洛伦佐预测器和均值积分洛伦佐预测，设计了一种混合洛伦佐预测方法。

### Optimizing error-bounded lossy compression for scientific data by dynamic spline interpolation
[web](https://ieeexplore.ieee.org/abstract/document/9458791)
2021 IEEE 37th International Conference on Data Engineering (ICDE)
引用数：98

是对基于预测的压缩器进行的改进，提出了一种具有一系列优化策略的动态样条插值方法，可以显着提高数据预测精度，从而大大提高压缩质量。

#### An efficient transformation scheme for lossy data compression with point-wise relative error bound
[web](https://ieeexplore.ieee.org/abstract/document/8514879/) 
2018 IEEE International Conference on Cluster Computing (CLUSTER)
引用数：86

本文提出了一种转化策略，
摘要：由于科​​学应用的执行规模不断增加，如何有效地存储海量数据已成为一个严重的问题。科学数据规模的显着减小可以有效减轻 I/O 负担并节省大量存储空间。由于无损压缩机的压缩比有限，因此误差控制的有损压缩机已被研究多年。然而，现有的误差控制有损压缩器主要关注绝对误差界限，无法满足用户的逐点相对误差界限等多样化需求。尽管一些最先进的有损压缩器支持逐点相对误差界限，但由于其设计的限制以及本地数据区域中可能出现的尖峰数据变化，压缩率通常较低。在这项工作中，我们提出了一种专门的、有效的方法来基于逐点相对误差界限执行压缩，其压缩比比现有解决方案提供的更高。我们的贡献是三重的。 (1) 我们利用一种专门的转换方案，可以将逐点相对误差有界压缩问题转换为绝对误差有界压缩问题。我们还从理论上和实验上分析了我们的转换方案的实际特性。 (2) 我们在两种最流行的绝对误差有界有损压缩器 SZ 和 ZFP 中实现对数变换。 (3) 我们在一台拥有多达 4,096 个内核和 12 TB 数据的超级计算机上使用跨不同科学领域的多个实际应用数据来评估我们的解决方案。实验表明，与第二好的有损压缩器相比，我们的解决方案分别实现了超过 1.38 倍的转储和 1.31 倍的加载性能。