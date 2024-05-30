
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

也是对科学计算产生的数据进行压缩压缩，
我们提出了一种自适应有损压缩框架，它可以更有效地压缩具有相对较大误差范围的科学数据集。特别地，我们将整个数据集分割成多个不重叠的块，并根据它们的数据特征选择最适合的预测方法。
我们开发了新的预测方法，对于具有相对较大误差范围的有损压缩特别有效。一方面，我们通过结合经典的洛伦佐预测器[6]和基于最稠均值的数据逼近方法（也称为均值积分洛伦佐预测），开发了一种混合洛伦佐预测方法。另一方面，我们开发了一种线性回归方法，在这种情况下可以获得更高的预测精度，因为设计超出了必须在预测中使用解压缩值的限制。
我们探索如何根据压缩过程中跨块的数据特征自适应且高效地选择最佳拟合预测方法。我们还提出了两种优化策略，可以进一步提高预测精度和量化效率。