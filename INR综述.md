Implicit Neural Representations（INR） 可以看成一种隐式学习某种映射 $f(\mathbf{x})\to \mathbf{y}$ 的方法，使用神经网络去逼近真实的映射 $f$。INR 最先在场景渲染方面大放异彩[^1], 他们隐式训练了一个把观测视角 $\theta,\phi$ 和观测位置 $x,y,z$ 映射到色彩和体密度的网络，来高保真地重建 3D 场景。Müller 等人为 INR 网络引入了多级的特征网格 (Feature grid)，使用空间坐标先去该网格中哈希索引到特征，再输入紧随的MLP网络中，以空间换时间的做法加速了INR 的训练过程。Sitzmann 为 INR 配备了周期性的激活函数（被称为 SIREN 网络），进一步提高了 INR 对于高频信号的重建能力。

INR 天然适合模拟各种不同模态的信号，其高保真以及紧凑表达的特性也为信号压缩带来了新的希望。接下来我们会从图像压缩与视频压缩两个方面来介绍 INR 的研究状况。

# 图像压缩
INR 在图像压缩任务中，普遍用来学习 $f(p_{x},p_{y})\to (R,G,B)$ 形式的信号，其中 $p_{x},p_{y}$ 是像素坐标，$R,G,B$ 是对应的像素值，$f$ 即图像所表示的真实的离散的信号映射。神经网络 $g$ 通过这一些离散的点，去学习一个连续的、逼近的信号映射。
COIN[^4] 首次把 INR 用作图像压缩，他们使用 MLP 构建这样的映射，对每张图像进行过拟合训练对应的网络，最后量化与存储网络的参数，这就完成了图像的压缩。尽管网络结构很简单，也没有对参数的分布进行学习，COIN 在低比特率下也超过了 JPEG。其后的一些改进围绕着更紧凑的网络设计、图像模型一对一问题的解决、模型瘦身（减枝、量化、编码）来进行。
COIN++[^5]把 COIN 中的网络替换成了 SIREN，并且采用了 meta-learning 的方法，不再为每张图像学习一个网络，而是先学习一个固定不变的网络，每张图像再单独学习其隐藏层额外的偏执向量，作为对中间状态的调制，减少了训练的用时以及码率的大小。Pham[^7]等人采用了和 COIN++类似的方法，不同的是，调制信息不再是一些可学习的向量，而是原图像经由另一个网络来生成的。Strümpler[^6] 同样也使用了 meta-learning 的方法以及 SIREN 结构，不同的是，他们对单张图像进行过拟合训练之后，会编码发送参数 $\theta$ 与那个不变的网络参数 $\theta_{0}$ 的差。Sanjeet[^]


[^1]: Ben Mildenhall, Pratul P. Srinivasan, Matthew Tancik, Jonathan T. Barron, Ravi Ramamoorthi, and Ren Ng. 2020. NeRF: Representing Scenes as Neural Radiance Fields for View Synthesis. In ECCV.
[^2]: Müller T, Evans A, Schied C, et al. Instant neural graphics primitives with a multiresolution hash encoding[J]. ACM transactions on graphics (TOG), 2022, 41(4): 1-15.
[^3]: Sitzmann V, Martel J, Bergman A, et al. Implicit neural representations with periodic activation functions[J]. Advances in neural information processing systems, 2020, 33: 7462-7473.
[^4]: Dupont E, Goliński A, Alizadeh M, et al. Coin: Compression with implicit neural representations[J]. arXiv preprint arXiv: 2103.03123, 2021.
[^5]: Dupont E, Loya H, Alizadeh M, et al. Coin++: Neural compression across modalities[J]. arXiv preprint arXiv: 2201.12904, 2022.
[^6]: Strümpler Y, Postels J, Yang R, et al. Implicit neural representations for image compression[C]//European Conference on Computer Vision. Cham: Springer Nature Switzerland, 2022: 74-91.
[^7]: Pham T, Yang Y, Mandt S. Autoencoding implicit neural representations for image compression[C]//ICML 2023 Workshop Neural Compression: From Information Theory to Applications. 2023.
[^8]: Sanjeet S, Hosseinalipour S, Xiong J, et al. Breaking the Barriers of One-to-One Usage of Implicit Neural Representation in Image Compression: A Linear Combination Approach with Performance Guarantees[J]. IEEE Internet of Things Journal, 2024.
