Implicit Neural Representations (INR) 最先在场景渲染方面大放异彩[^1], 他们隐式训练了一个把观测视角 $\theta,\phi$ 和观测位置 $x,y,z$ 映射到色彩和体密度的网络，来高保真地重建 3D 场景。Müller 等人为 INR 网络引入了多级的特征网格 (Feature grid)，使用空间坐标先去该网格中哈希索引到特征，再输入紧随的MLP网络中，以空间换时间的做法加速了INR 的训练过程。Sitzmann 为 INR 配备了周期性的激活函数（被称为 SIREN 网络），进一步提高了 INR 对于高频信号的重建能力。

INR 的高保真和


[^1]: Ben Mildenhall, Pratul P. Srinivasan, Matthew Tancik, Jonathan T. Barron, Ravi Ramamoorthi, and Ren Ng. 2020. NeRF: Representing Scenes as Neural Radiance Fields for View Synthesis. In ECCV.
[^2]: Müller T, Evans A, Schied C, et al. Instant neural graphics primitives with a multiresolution hash encoding[J]. ACM transactions on graphics (TOG), 2022, 41(4): 1-15.
[^3]: Sitzmann V, Martel J, Bergman A, et al. Implicit neural representations with periodic activation functions[J]. Advances in neural information processing systems, 2020, 33: 7462-7473.