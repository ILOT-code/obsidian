
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

SZ 和 ZFP 在大数据压缩领域是领先的方法。本文分析了他们的原理，提出了一种高效的在线、低开销选择算法，该算法可以在早期处理阶段准确预测两个压缩器的压缩质量，并为每个数据字段选择最适合的压缩器。

### Sz3: A modular framework for composing prediction-based error-bounded lossy compressors
[web](https://ieeexplore.ieee.org/abstract/document/9866018/)
IEEE Transactions on Big Data, 2022
引用数：65

本文提出了一个模块化和可组合的框架 Sz 3,  用户可以通过精心设计的模块定制压缩管道中的任何阶段，包括预处理、预测、量化、编码和无损压缩

### Z-checker: A framework for assessing lossy compression of scientific data
[web](( https://journals.sagepub.com/doi/abs/10.1177/1094342017737147 )
The International Journal of High Performance Computing Applications, 2019
引用次数：64

本文设计了一个 Z-checker 的有损压缩通用框架，
由于当今的科学模拟和实验产生大量数据，有损数据压缩器允许用户在压缩过程中控制精度损失，是显着减小数据大小的相关解决方案。然而，有损压缩器开发人员和用户缺少一种工具来探索科学数据集的特征并以系统且可靠的方式了解压缩后的数据变化。为了解决这个问题，我们设计并实现了一个名为 Z-checker 的通用框架。一方面，Z-checker 结合了一系列数据分析组件来进行数据压缩。另一方面，Z-checker 是作为开源社区工具实现的，用户和开发人员可以根据自己的额外分析需求做出贡献并添加新的分析组件。在本文中，我们对现有有损压缩机进行了调查。然后，我们描述了 Z-checker 的设计框架，其中我们集成了先前工作中提出的评估指标以及其他分析工具。具体来说，对于有损压缩器开发人员来说，Z-checker 可用于表征任何数据集的关键属性（例如熵、分布、功率谱、主成分分析和自相关），以改进压缩策略。对于有损压缩用户，Z-checker可以检测压缩质量（压缩比和比特率），并提供各种全局失真分析，将原始数据与解压缩数据进行比较（峰值信噪比、归一化均方误差、码率-失真、速率压缩误差、频谱、分布和导数）以及压缩误差的统计分析（最大、最小和平均误差；自相关；以及误差分布）。 Z-checker 可以以粗粒度（整个数据集）或细粒度（通过用户定义的块）执行分析，以便用户和开发人员可以为数据集的不同部分选择最适合的自适应压缩器。 Z-checker 具有可视化界面，除了数据集的一些基本视图（例如时间序列）之外，还显示所有分析结果。据我们所知，Z-checker 是第一个旨在全面评估科学数据集有损压缩的工具。
### SDRBench: Scientific data reduction benchmark for lossy compressors
[web](https://ieeexplore.ieee.org/abstract/document/9378449/)
2020 IEEE international conference on big data (Big Data)
引用数：61

### Exploring autoencoder-based error-bounded compression for scientific data
[web](https://ieeexplore.ieee.org/abstract/document/9555941/)
2021 IEEE International Conference on Cluster Computing (CLUSTER)
引用数：34

### Significantly improving lossy compression quality based on an optimized hybrid prediction model]
[web](https://dl.acm.org/doi/abs/10.1145/3295500.3356193)
(book)Proceedings of the International Conference for High Performance Computing, Networking, Storage and Analysis, 2019
引用数：34

### Improving performance of data dumping with lossy compression for scientific simulation
[web]( https://ieeexplore.ieee.org/abstract/document/8891037/
2019 IEEE International Conference on Cluster Computing (CLUSTER)
引用数：30
### Fixed-PSNR lossy compression for scientific data
[web](https://ieeexplore.ieee.org/abstract/document/8514891/)
2018 IEEE International Conference on Cluster Computing (CLUSTER)
引用数：26

### Optimizing lossy compression with adjacent snapshots for n-body simulation data
[web](https://scholar.google.com.hk/citations?view_op=view_citation&hl=zh-CN&user=AvanGFkAAAAJ&cstart=20&pagesize=80&citation_for_view=AvanGFkAAAAJ:NJdIkVacNvsC)
2018 IEEE International Conference on Big Data (Big Data), 428-437
引用数：20


### Mdz: An efficient error-bounded lossy compressor for molecular dynamics
[web](https://ieeexplore.ieee.org/abstract/document/9835212/)
作者
2022 IEEE 38th International Conference on Data Engineering (ICDE)
引用数：18
