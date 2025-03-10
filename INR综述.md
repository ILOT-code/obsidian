Implicit Neural Representations (INR) 最先在场景渲染方面大放异彩[^1], 他们隐式训练了一个把观测视角 $\theta,\phi$ 和观测位置 $x,y,z$ 映射到色彩和体密度的网络，来高保真地重建 3D 场景。Müller 等人为 INR 网络引入了多级的特征网格 (Feature grid)，使用空间坐标先去该网格中哈希索引到特征，再输入紧随的MLP网络中




[^1]: Ben Mildenhall, Pratul P. Srinivasan, Matthew Tancik, Jonathan T. Barron, Ravi Ramamoorthi, and Ren Ng. 2020. NeRF: Representing Scenes as Neural Radiance Fields for View Synthesis. In ECCV.
[^2]: Müller T, Evans A, Schied C, et al. Instant neural graphics primitives with a multiresolution hash encoding[J]. ACM transactions on graphics (TOG), 2022, 41(4): 1-15.