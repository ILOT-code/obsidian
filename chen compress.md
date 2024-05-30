
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

本文提出了一种转化策略，将具有逐点相对误差限制的压缩问题，转化为绝对误差限制压缩问题。

### Optimizing lossy compression rate-distortion from automatic online selection between SZ and ZFP
[web](https://ieeexplore.ieee.org/abstract/document/8621017/)
IEEE Transactions on Parallel and Distributed Systems 2019
引用数：75

SZ 和 ZFP 在大数据压缩领域是领先的方法。本文分析了他们的原理，
摘要：随着高性能计算应用程序产生的科学数据量不断增加，由于存储空间容量有限以及 I/O 或网络在写入/读取或传输数据时存在潜在瓶颈，显着减小数据大小至关重要。 SZ 和 ZFP 是两个领先的 BSD 许可开源 C/C++ 库，用于支持高吞吐量读写随机访问的压缩浮点数组。然而，它们在不同数据集和某些数据集的不同字段之间的性能并不一致，这提出了在 SZ 和 ZFP 之间以最小的开销自动在线（在压缩期间）选择的需要。在本文中，自动选择优化了率失真，这是一种基于信噪比的重要统计质量指标。为了优化率失真，我们研究了 SZ 和 ZFP 的原理。然后，我们提出了一种高效的在线、低开销选择算法，该算法可以在早期处理阶段准确预测两个压缩器的压缩质量，并为每个数据字段选择最适合的压缩器。我们将选择算法实现到开源库中，并在具有 1,024 个内核的并行环境中针对普通 SZ 和 ZFP 评估我们提出的解决方案的有效性。对代表约 100 个字段的三个数据集的评估结果表明，我们的选择算法在相同水平的数据失真下将压缩率提高了 70%，因为非常准确地选择了最佳拟合压缩器（约 99%），并且开销很小（更少实验中低于 7%）。
