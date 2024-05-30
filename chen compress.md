
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

本文设计了一个 Z-checker 框架，为有损压缩器开发人员和用户提供探索数据集的特征并以系统且可靠的方式了解压缩后数据变化的工具，是第一个旨在全面评估科学数据集有损压缩的工具。

### SDRBench: Scientific data reduction benchmark for lossy compressors
[web](https://ieeexplore.ieee.org/abstract/document/9378449/)
2020 IEEE international conference on big data (Big Data)
引用数：61

本文建立了标准的压缩评估基准，包含跨不同领域的大量真实科学数据集，为研究人员和用户以公平、便捷的方式评估和比较压缩器提供帮助。

### Exploring autoencoder-based error-bounded compression for scientific data
[web](https://ieeexplore.ieee.org/abstract/document/9555941/)
2021 IEEE International Conference on Cluster Computing (CLUSTER)
引用数：34

摘要：误差有限有损压缩正在成为当今科学项目成功不可或缺的技术，这些项目在模拟或仪器数据采集过程中会产生大量数据。它不仅可以显着减少数据大小，还可以根据用户指定的误差范围控制压缩误差。自动编码器 (AE) 模型已广泛应用于图像压缩中，但很少有基于 AE 的压缩方法支持科学应用高度需要的误差限制特征。为了解决这个问题，我们探索使用卷积自动编码器来改进科学数据的误差有限有损压缩，并做出以下三个关键贡献。 (1) 我们深入研究了各种自动编码器模型的特性，并根据 SZ 模型开发了一个基于误差有界自动编码器的框架。 (2) 我们优化了我们设计的基于 AE 的误差限制压缩框架中主要阶段的压缩质量，微调了块大小和潜在大小，并优化了潜在向量的压缩效率。 (3) 我们使用五个真实世界的科学数据集评估我们提出的解决方案，并将它们与其他六个相关工作进行比较。实验表明，我们的解决方案在我们测试的所有压缩机中表现出非常有竞争力的压缩质量。从绝对值来看，在高压缩比的情况下，与 SZ 2.1 和 ZFP 相比，它可以获得更好的压缩质量（相同数据失真情况下压缩比提高 100%∼800%）。
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
