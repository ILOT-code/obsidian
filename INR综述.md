Implicit Neural Representations（INR） 可以看成一种隐式学习某种映射 $f(\mathbf{x})\to \mathbf{y}$ 的方法，使用神经网络去逼近真实的映射 $f$。INR 最先在场景渲染方面大放异彩[^1], 他们隐式训练了一个把观测视角 $\theta,\phi$ 和观测位置 $x,y,z$ 映射到色彩和体密度的网络，来高保真地重建 3D 场景。Müller 等人为 INR 网络引入了多级的特征网格 (Feature grid)，使用空间坐标先去该网格中哈希索引到特征，再输入紧随的MLP网络中，以空间换时间的做法加速了INR 的训练过程。Sitzmann 为 INR 配备了周期性的激活函数（被称为 SIREN 网络），进一步提高了 INR 对于高频信号的重建能力。

INR 天然适合模拟各种不同模态的信号，其高保真以及紧凑表达的特性也为信号压缩带来了新的希望。接下来我们会从图像压缩与视频压缩两个方面来介绍 INR 的研究状况。

# 图像压缩
INR 在图像压缩任务中，普遍用来学习 $f(p_{x},p_{y})\to (R,G,B)$ 形式的信号，这被称为 pixel-wise，其中 $p_{x},p_{y}$ 是像素坐标，$R,G,B$ 是对应的像素值，$f$ 即图像所表示的真实的离散的信号映射。神经网络 $g$ 通过这一些离散的点，去学习一个连续的、逼近的信号映射。

COIN[^4] 首次把 INR 用作图像压缩，他们使用 MLP 构建这样的映射，对每张图像进行过拟合训练对应的网络，最后量化与存储网络的参数，这就完成了图像的压缩。尽管网络结构很简单，也没有对参数的分布进行学习，COIN 在低比特率下也超过了 JPEG。其后的一些改进围绕着更紧凑的网络设计、图像模型一对一问题的解决、模型瘦身（减枝、量化、编码）来进行。

COIN++[^5]把 COIN 中的网络替换成了 SIREN，并且采用了 meta-learning 的方法，不再为每张图像学习一个网络，而是先学习一个固定不变的网络，每张图像再单独学习其隐藏层额外的偏执向量，作为对中间状态的调制，减少了训练的用时以及码率的大小。Pham[^7]等人采用了和 COIN++类似的方法，不同的是，调制信息不再是一些可学习的向量，而是原图像经由另一个网络来生成的。Strümpler[^6] 同样也使用了 meta-learning 的方法以及 SIREN 结构，不同的是，他们对单张图像进行过拟合训练之后，会编码发送参数 $\theta$ 与那个不变的网络参数 $\theta_{0}$ 的差。除了 meta-learning 之外，Sanjeet[^8]提出了一种新颖的训练方法，使得用 $N$ 个 INR 网络来表达 $M(M>N)$ 个图像成为可能，其中，每个图像的网络参数都是这 $N$ 个网络的线性组合。他们理论证明了其方法的收敛性并进行了实验验证。

Catania[^9]对 SIREN 的激活函数 $\sin(\omega_{0}(\mathbf{W}\mathbf{x}+\mathbf{b}))$ 进行了改进，他们通过一个额外的网络，为主干网络的每个激活函数的周期学习一个偏置，改变了原本周期固定的激活模式，即使不使用 meta-learning 方法，性能也超过了 COIN++。COOL-CHIC[^10] 放弃了直接从像素坐标到像素值的映射，而是和 Müller[^2]类似，引入了多级的特征网格，再索引到对应特征之后，再输入其后的INR网络。另外，他们基于拉普拉斯分布，为特征网格的编码设计了自回归的熵模型，并为网络参数设计了无参的熵模型。C3[^11]在 COOL-CHIC 的基础上，对量化策略、训练策略、学习率更新策略等进行了一系列的优化，得到了更强的方案。Zhang[^12]等人在 COOL-CHIC 的基础上，设计了可微分的量化策略，并用卷积网络搭建了其后的INR 网络，加强了邻近像素间的联系。Gao[^13]等人为 INR 压缩范式引入了结构性的正则化项，这是通过使用训练好的图像分割网络，比较真实图片与INR输出的图片对应的分割掩码的不同来得到，他们在小范围数据上验证了其有效性。

Ramirez[^14]为 COIN 的训练引入一个 $L_{0}$ 约束损失，来促进网络的稀疏，以降低码率。RQAT-INR[^15]设计了一种无参的基于高斯分布的熵模型，来估计量化后网络参数的概率值。并且使用了量化感知的训练方案，并加入了量化带来的模型精度降低的损失函数，缩小了量化带来的不利影响。

# 视频压缩

不同与图像压缩任务，视频压缩一般采用 $f(t)\to frame$（image-wise）这种一次生成整个帧，或者是 $f(patch_{x},patch_{y},t)\to patch$ （patch-wise）一次生成一个 patch 的形式。

NeRV[^16]最先使用image-wise形式来进行视频压缩，向网络中输入帧索引来获得整张图像，网络主要由卷积层搭建，训练完毕后会对模型进行减枝量化编码处理。不同于 NeRV 向网络输入单纯的帧索引，E -NeRV[^17]把网络分解成了时间相关和空间相关的两个分支，并最后进行融合。这种设计加速了网络的训练并提升了性能。


# 参考文献

[^1]: Ben Mildenhall, Pratul P. Srinivasan, Matthew Tancik, Jonathan T. Barron, Ravi Ramamoorthi, and Ren Ng. 2020. NeRF: Representing Scenes as Neural Radiance Fields for View Synthesis. In ECCV.
[^2]: Müller T, Evans A, Schied C, et al. Instant neural graphics primitives with a multiresolution hash encoding[J]. ACM transactions on graphics (TOG), 2022, 41(4): 1-15.
[^3]: Sitzmann V, Martel J, Bergman A, et al. Implicit neural representations with periodic activation functions[J]. Advances in neural information processing systems, 2020, 33: 7462-7473.
[^4]: Dupont E, Goliński A, Alizadeh M, et al. Coin: Compression with implicit neural representations[J]. arXiv preprint arXiv: 2103.03123, 2021.
[^5]: Dupont E, Loya H, Alizadeh M, et al. Coin++: Neural compression across modalities[J]. arXiv preprint arXiv: 2201.12904, 2022.
[^6]: Strümpler Y, Postels J, Yang R, et al. Implicit neural representations for image compression[C]//European Conference on Computer Vision. Cham: Springer Nature Switzerland, 2022: 74-91.
[^7]: Pham T, Yang Y, Mandt S. Autoencoding implicit neural representations for image compression[C]//ICML 2023 Workshop Neural Compression: From Information Theory to Applications. 2023.
[^8]: Sanjeet S, Hosseinalipour S, Xiong J, et al. Breaking the Barriers of One-to-One Usage of Implicit Neural Representation in Image Compression: A Linear Combination Approach with Performance Guarantees[J]. IEEE Internet of Things Journal, 2024.
[^9]: Catania L, Allegra D. Nif: A fast implicit image compression with bottleneck layers and modulated sinusoidal activations[C]//Proceedings of the 31st ACM International Conference on Multimedia. 2023: 9022-9031.
[^10]: Ladune T, Philippe P, Henry F, et al. Cool-chic: Coordinate-based low complexity hierarchical image codec[C]//Proceedings of the IEEE/CVF International Conference on Computer Vision. 2023: 13515-13522.
[^11]: Kim H, Bauer M, Theis L, et al. C3: High-performance and low-complexity neural compression from a single image or video[C]//Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2024: 9347-9358.
[^12]: Zhang G, Zhang X, Tang L. Enhanced quantified local implicit neural representation for image compression[J]. IEEE Signal Processing Letters, 2023, 30: 1742-1746.
[^13]: Gao H, Gan W, Sun Z, et al. Sinco: A novel structural regularizer for image compression using implicit neural representations[C]//ICASSP 2023-2023 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP). IEEE, 2023: 1-5.
[^14]: Ramirez J, Gallego-Posada J. L $_0$ onie: Compressing COINs with L $_0$-constraints[J]. arXiv preprint arXiv: 2207.04144, 2022.
[^15]: Damodaran B B, Balcilar M, Galpin F, et al. Rqat-inr: Improved implicit neural image compression[C]//2023 Data Compression Conference (DCC). IEEE, 2023: 208-217.
[^16]: Chen H, He B, Wang H, et al. Nerv: Neural representations for videos[J]. Advances in Neural Information Processing Systems, 2021, 34: 21557-21568.
[^17]: Li Z, Wang M, Pi H, et al. E-nerv: Expedite neural video representation with disentangled spatial-temporal context[C]//European Conference on Computer Vision. Cham: Springer Nature Switzerland, 2022: 267-284.
[^18]: Bai Y, Dong C, Wang C, et al. Ps-nerv: Patch-wise stylized neural representations for videos[C]//2023 IEEE International Conference on Image Processing (ICIP). IEEE, 2023: 41-45.
[^19]: Maiya S R, Girish S, Ehrlich M, et al. Nirvana: Neural implicit representations of videos with adaptive networks and autoregressive patch-wise modeling[C]//Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2023: 14378-14387.
[^20]: Rho D, Cho J, Ko J H, et al. Neural residual flow fields for efficient video representations[C]//Proceedings of the Asian Conference on Computer Vision. 2022: 3447-3463.
[^21]: Lee J C, Rho D, Ko J H, et al. Ffnerv: Flow-guided frame-wise neural representations for videos[C]//Proceedings of the 31st ACM International Conference on Multimedia. 2023: 7859-7870.
[^22]: Chen H, Gwilliam M, Lim S N, et al. Hnerv: A hybrid neural representation for videos[C]//Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2023: 10270-10279.
[^23]: Zhao Q, Asif M S, Ma Z. Dnerv: Modeling inherent dynamics via difference neural representation for videos[C]//Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2023: 2031-2040.
[^24]: Gomes C, Azevedo R, Schroers C. Video compression with entropy-constrained neural representations[C]//Proceedings of the IEEE/CVF conference on computer vision and pattern recognition. 2023: 18497-18506.
[^25]: He B, Yang X, Wang H, et al. Towards scalable neural representation for diverse videos[C]//Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2023: 6132-6142.
[^26]: Kwan H M, Gao G, Zhang F, et al. Hinerv: Video compression with hierarchical encoding-based neural representation[J]. Advances in Neural Information Processing Systems, 2023, 36: 72692-72704.
[^27]: Saethre J E, Azevedo R, Schroers C. Combining Frame and GOP Embeddings for Neural Video Representation[C]//Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2024: 9253-9263.
[^28]: Xu Y, Feng X, Qin F, et al. Vq-nerv: A vector quantized neural representation for videos[J]. arXiv preprint arXiv: 2403.12401, 2024.
[^29]: Zhang X, Yang R, He D, et al. Boosting neural representations for videos with a conditional decoder[C]//Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2024: 2556-2566.
[^30]: Kwan H M, Gao G, Zhang F, et al. NVRC: Neural video representation compression[J]. arXiv preprint arXiv:2409.07414, 2024.


