
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