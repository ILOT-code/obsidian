[Fetching Title#x4qe](https://www.zhangzhenhu.com/aigc/%E6%89%A9%E6%95%A3%E6%A6%82%E7%8E%87%E6%A8%A1%E5%9E%8B.html)
references:[VAE](https://kexue.fm/archives/5253) [1](https://blog.csdn.net/a312863063/article/details/87953517)[2](https://juejin.cn/post/7028423906998353951)[3](https://blog.csdn.net/qq_41196612/article/details/109528221).


## VAE介绍

变分自编码器 (Variational Auto-Encoders, VAE) 属于生成模型家族。VAE 的生成器能够利用连续潜在空间的矢量产生有意义的输出。通过潜在矢量探索解码器输出的可能属性。

在 GAN 中，重点在于如何得出近似输入分布的模型。 VAE尝试对可解耦的连续潜在空间中的输入分布进行建模。

在 VAE 中，重点在于潜编码的变分推理。因此，VAE 为潜在变量的学习和有效贝叶斯推理提供了合适的框架。

在结构上，VAE 与自编码器相似。它也由编码器（也称为识别或推理模型）和解码器（也称为生成模型）组成。 VAE 和自编码器都试图在学习潜矢量的同时重建输入数据。但是，与自编码器不同，VAE 的潜在空间是连续的，并且解码器本身被用作生成模型。

### VAE原理

在生成模型中，使用神经网络来逼近输入的真实分布：

$$
x\sim P_\theta(x)\quad(1)
$$

其中，$\theta$ 表示模型参数。
根据贝叶斯定理，改写为：
$$
\begin{equation} \begin{aligned}
P_\theta(x)&=\int P_\theta(x,z)dz \\
&=\int P_\theta(x|z)P(z)dz \\
&=\int P_\theta(z|x)P(x)dz
\end{aligned}\end{equation} \\

\tag{2}
$$
其中,$P(z)$ 是$z$ 的先验分布。它不以任何观察为条件。一般而言，在VAE中，$P(z)$是标准正态分布，这是因为对任何$x$的分布$P(x)$，始终可以在此前提假设下，选择恰当的$\theta$，来使（2）的第二条等式成立，如下图：
![[Pasted image 20231018212439.png]]

这样的选择能简化计算。另外，值得注意的是，对于$P(z)$，其实并不需要强制其是正态分布，但需强制$P_\theta(z|x)$是正态分布。我们可以看到，在整个VAE推导中，$P(z)$的分布没有限制，但网络训练过程中会趋向让$P_\theta(z|x)$变为标准正太分布（后文会提到）。实际上由方程 $P(Z)=\int P(Z|X)P(X)dx$ 限定了$P(Z)$，它并不一定会是正太分布。但是当$P_\theta(x|z)$趋向于标准正太分布时，
$$
P(Z)=\int P(Z|X)P(X)dx=\int\mathcal{N}(0,I)P(X)dx=\mathcal{N}(0,I)\int P(X)dx=\mathcal{N}(0,I) \tag{3}
$$

### VAE流程概览
给定一个真实样本$X_k$, 我们假设存在一个专属于 $X_k$ 的分布$P(Z|X_k)$ (学名叫后验分布), 并进一步假设这个分布是(独立的、多元的) 正态分布。为什么要强调“专属”呢? 因为我们后面要训练一个生成器$X=g(Z)$,希望能够把从分布$P(Z|X_k)$采样出来的一个$Z_k$还原为$X_k$。

这时候每一个$X_k$都配上了一个专属的正态分布,才方便后面的生成器做还原。但这样有多少个$X$就有多少个正态分布了。我们知道正态分布有两组参数: 均值$\mu$和方差$\sigma^2$(多元的话,它们都是向量) , 那我怎么找出专属于$X_k$的正态分布$P(Z|X_k)$的均值和方差呢? 好像并没有什么直接的思路。那好吧, 那我就用神经网络来拟合出来吧! 这就是神经网络时代的哲学。

于是我们构建两个神经网络$\mu_k=f_1(X_k),\log\sigma_k^2=f_2(X_k)$来算它们了。我们选择拟合$\log\sigma_k^2$而不是直接拟合$\sigma_k^2$,是因为$\sigma_k^2$总是非负的,需要加激活函数处理,而拟合$\log\sigma_k^2$不需要加激活函数,因为它可正可负。到这里, 我能知道专属于$X_k$的均值和方差了, 也就知道它的正态分布长什么样了,然后从这个专属分布中采样一个$Z_k$出来,然后经过一个生成器得到$\hat{X}_k=g(Z_k)$,现在我们可以放心地最小化$\hat{D}(\hat{X}_k,X_k)^2$, 因为$Z_k$是从专属$X_k$的分布中采样出来的,这个生成器应该要把开始的$X_k$还原回来。于是可以画出VAE的示意图

![[Pasted image 20231018231523.png]]

在（3）的假设下：
![[Pasted image 20231018234644.png]]
### 变分推理

为了使 $P_\theta(z|x)$ 易于处理, VAE 引入了变分推断模型 (编码器) :

$$
Q_\phi(z|x)\approx P_\theta(z|x) \tag{5}
$$

$Q_\phi(z|x)$ 可很好地估计 $P_\theta(z|x)$。它既可以参数化又易于处理。可以通过深度神经网络优化参数 $\varphi$ 来近似$Q_\phi(z|x)$。在VAE中，限定$Q_\phi(z|x)$ 为维度间独立的多元高斯分布:

$$
Q_\phi(z|x)=\mathcal{N}(z;\mu(x),diag(\sigma(x)^2)) \tag{6}
$$

均值 $\mu(x)$ 和标准差 $\sigma(x)$ 均由编码器神经网络使用输入数据计算得出。对角矩阵表示$z$中的元素间是相互独立的。

推理模型 $Q_\phi(z|x)$ 从输入 $x$ 生成潜矢量 $z$。$Q_\phi(z|x)$ 类似于自编码器模型中的编码器。另一方面$P_\theta(x|z)$ 从潜码$z$重建输入。$P_\theta(x|z)$ 的作用类似于自编码器模型中的解码器。
### VAE核心方程

$Q_\phi(z|x)$ 是$P_\theta(z|x)$ 的估计值, 则以KL散度确定这两个条件密度之间的距离

$$
D_{KL}(Q_\phi(z|x)\|P_\theta(z|x))=\mathbb{E}_{z\sim Q}[logQ_\phi(z|x)-logP_\theta(z|x)] \tag{7}
$$

使用贝叶斯定理:

$$
P_\theta(z|x)=\frac{P_\theta(x|z)P_\theta(z)}{P_\theta(x)} \tag{8}
$$

通过公式(8)改写公式(7),同时由于$logP_\theta(x)$ 不依赖于$z\sim Q$，得到：

$$
D_{KL}(Q_{\phi}(z|x)\|P_{\theta}(z|x))=\mathbb{E}_{z\sim Q}[logQ_{\phi}(z|x)-logP_{\theta}(x|z)-logP_{\theta}(z)]+logP_{\theta}(x) \tag{9}
$$

重排上式并由:

$$
\mathbb{E}_{z\sim Q}[logQ_{\phi}(z|x)-logP_{\theta}(z)]=D_{KL}(Q_{\phi}(z|x)\|P_{\theta}(z)) \tag{10}
$$

得到VAE的核心：

$$
logP_{\theta}(x)-D_{KL}(Q_{\phi}(z|x)\|P_{\theta}(z|x))=\mathbb{E}_{z\sim Q}[logP_{\theta}(x|z)]-D_{KL}(Q_{\phi}(z|x)\|P_{\theta}(z)) \tag{11}
$$

### 优化方法

$x$是输入的图像向量，根据极大似然，$P_\theta (x)$ 值越大越好。由于KL散度非负，所以，右边的式子是$logP_{\theta}(x)$的下限，ELBO。

看右边第一项，它实际上表达了重构图像的质量，将它展开就能看出来：
$$
\begin{equation} \begin{aligned}
\mathbb{E}_{z\sim Q}[logP_{\theta}(x|z)] &=\int Q(z|x)P_\theta (x|z) dz 
\end{aligned}\end{equation} \tag{12}
$$
在给定了一张图片$x$后，上式描述了经过编码（$Q(z|x)$）和解码（$P_\theta (x|z)$）后，得到的图像是原图像 $x$ 的概率，这就是对重构图像质量的描述，它越大越好。可以用MSE等评价值指标来衡量，最大化该项意味着将重建损失 $\mathcal{L}_R$ 最小化。

看右边第二项， $-D_{KL}(Q_\phi(z|x)||P_\theta(z))$，描述了两个分布的距离，由于$Q_\phi$ 是高斯分布。通常 $P_\theta(z)=P(z)=$ $\mathcal{N}(0,1)$,也是均值为 0 且标准偏差等于 1.0 的高斯分布。这一项可以简化为:

$$
-D_{KL}(Q_\phi(z|x)\|P_\theta(z))=\frac12\sum_{j=0}^{J}(1+log(\sigma_j)^2-(\mu_j)^2-(\sigma_j)^2) \tag{13}
$$
> 上面的式子可以推导得到：
> 由于我们考虑的是各分量独立的多元正态分布，因此只需要推导一元正态分布的情形即可，根据定义我们可以写出：
> $$
> \begin{aligned}
> &KL\Big(N(\mu,\sigma^2)\Big\Vert N(0,1)\Big) \\
> &=\int\frac1{\sqrt{2\pi\sigma^2}}e^{-(x-\mu)^2/2\sigma^2}\left(\log\frac{e^{-(x-\mu)^2/2\sigma^2}/\sqrt{2\pi\sigma^2}}{e^{-x^2/2}/\sqrt{2\pi}}\right)dx \\
> &=\int\frac1{\sqrt{2\pi\sigma^2}}e^{-\left(x-\mu\right)^2/2\sigma^2}\log\left\{\frac1{\sqrt{\sigma^2}}\exp\left\{\frac12\left[x^2-(x-\mu)^2/\sigma^2\right]\right\}\right\}dx \\
> &=\frac12\int\frac1{\sqrt{2\pi\sigma^2}}e^{-(x-\mu)^2/2\sigma^2}\left[\left.-\log\sigma^2+x^2-(x-\mu)^2/\sigma^2\right]dx\right. 
> \end{aligned}
> $$
> 整个结果分为三项积分,第一项实际上就是$-\log\sigma^2$乘以概率密度的积分 (也就是1) , 所以结果是$-\log\sigma^2$; 第二项实际是正态分布的二阶矩, 熟悉正态分布的朋友应该都清楚正态分布的二阶矩为 $\mu^2+\sigma^2$; 而根据定义, 第三项实际上就是“-方差除以方差=-1”。所以总结果就是
> 
> $$
> \left.KL\left(N(\mu,\sigma^2)\right\Vert N(0,1)\right)=\frac12\left(-\log\sigma^2+\mu^2+\sigma^2-1\right)
> $$
> 这就得到证明


其中 $J$ 是 $z$ 的维数。和都是通过推理模型计算得到的关于 $x$ 的函数。要最大化 $-D_{KL}$ :则$\sigma_j\to1$ ,$\mu_j\to0$。也就是$Q(z|x)$向正态分布靠拢。$P(z)=\mathcal{N}(0,1)$ 的选择是由于各向同性单位高斯分布的性质, 可以给定适当的函数将其变形为任意分布。

KL 损失 $\mathcal{L}_{KL}$ 为$D_{KL}$。综上, VAE 损失函数定义为:
$$
\mathcal{L}_{VAE}=\mathcal{L}_{R}+\mathcal{L}_{KL} \tag{14}
$$


### 更合理的推导
实际上，我认为上面的推导将已知先验概率和使用参数来得到的概率混为一谈，下面引用另一个推导，注意符号的改动：

出发点依然没变,这里再重述一下。首先我们有一批数据样本$\{x_1,\ldots,x_n\}$, 其整体用$x$来描述, 我们希望借助隐变量$z$描述$x$的分布$\tilde{p}(x)$:

$$
q(x)=\int q(x|z)q(z)dz,\quad q(x,z)=q(x|z)q(z)
$$

这里$q(z)$是先验分布 (标准正态分布), 目的是希望$q(x)$能逼近$\tilde{p}(x)$。这样 (理论上) 我们既描述了$\tilde{p}(x)$,又得到了生成模型$q(x|z)$,一举两得。


直接来对$p(x,z)$进行近似是最为干脆的。具体来说, 定义$p(x,z)=\tilde{p}(x)p(z|x)$,我们设想用一个联合概率分布$q(x,z)$来逼近$p(x,z)$,那么我们用KL散度来看它们的距离:

$$
KL\left(p(x,z)\|q(x,z)\right)=\int\int p(x,z)\ln\frac{p(x,z)}{q(x,z)}dzdx
$$

KL散度是我们的终极目标,因为我们希望两个分布越接近越好,所以KL散度越小越好。当然,由于现在$p(x,z)$也有参数, 所以不单单是$q(x,z)$来逼近$p(x,z)$,$p(x,z)$也会主动来逼近$q(x,z)$, 两者是相互接近。

于是我们有


$$
\begin{aligned}
KL\Big(p(x,z)\Big\Vert q(x,z)\Big)& =\int\tilde{p}(x)\left[\int p(z|x)\ln\frac{\tilde{p}(x)p(z|x)}{q(x,z)}dz\right]dx \\
&=\mathbb{E}_{x\sim\tilde{p}\left(x\right)}\left[\int p(z|x)\ln\frac{\tilde{p}(x)p(z|x)}{q(x,z)}dz\right]
\end{aligned}
$$
这个式子还可以进一步简化,因为$\ln\frac{\tilde{p}(x)p(z|x)}{q(x,z)}=\ln\tilde{p}(x)+\ln\frac{p(z|x)}{q(x,z)}$,而

$$
\begin{aligned}
\mathbb{E}_{x\sim\bar{p}(x)}\left[\int p(z|x)\ln\tilde{p}(x)dz\right]=& \mathbb{E}_{x\sim\tilde{p}(x)}\left[\ln\tilde{p}(x)\int p(z|x)dz\right] \\
=&\mathbb{E}_{x\sim\bar{p}\left(x\right)}\left[\ln\tilde{p}(x)\right]
\end{aligned}
$$

注意这里的$\tilde{p}(x)$是根据样本$x_1,x_2,\ldots,x_n$确定的关于$x$的先验分布,尽管我们不一定能准确写出它的形式,但它是确定的、存在的,因此这一项只是一个常数,所以可以写出

$$
\mathcal{L}=KL\left(p(x,z)\|q(x,z)\right)-\text{常数}=\mathbb{E}_{x\sim\check{p}(x)}\left[\int p(z|x)\ln\frac{p(z|x)}{q(x,z)}dz\right]
$$

目前最小化$KL\left(p(x,z)\|q(x,z)\right)$也就等价于最小化$\mathcal{L}$。注意减去的常数为$\mathbb{E}_{x\sim\tilde{p}(x)}\left[\ln\tilde{p}(x)\right]$,所以$\mathcal{L}$拥有下界$-\mathbb{E}_{x\sim\bar{p}(x)}\left[\ln\tilde{p}(x)\right]\sim$注意到$\tilde{p}(x)$不一定是概率, 在连续情形时$\tilde{p}(x)$是概率密度、它可以大于1也可以小于1, 所以$-\mathbb{E}_{x\cdot\cdot\bar{p}(x)}[\ln\tilde{p}(x)]$不一定是非负, 即loss可能是负数。

到这里, 我们回顾初衷一一为了得到生成模型, 所以我们把$q(x,z)$写成$q(x|z)q(z)_{\prime}$ 于是就有

$$
\begin{aligned}\mathcal{L}=&\mathbb{E}_{x\sim\tilde{p}(x)}\left[\int p(z|x)\ln\frac{p(z|x)}{q(x|z)q(z)}dz\right]\\=&\mathbb{E}_{x\sim\tilde{p}(x)}\left[-\int p(z|x)\ln q(x|z)dz+\int p(z|x)\ln\frac{p(z|x)}{q(z)}dz\right]\end{aligned}
$$

再简明一点,那就是

$$
\begin{gathered}
\mathcal{L}= \left.\mathbb{E}_{x\sim\widetilde{\boldsymbol{p}}(x)}\left[\mathbb{E}_{z\sim\boldsymbol{p}(z|x)}[-\ln q(x|z)\right]+\mathbb{E}_{z\sim\boldsymbol{p}(z|x)}\left[\ln\frac{\boldsymbol{p}(z|x)}{q(z)}\right]\right] \\
= \left.\mathbb{E}_{x\sim\widetilde{\boldsymbol{p}}(x)}\left[\mathbb{E}_{z\sim\boldsymbol{p}(z|x)}\big[\left.-\ln q(x|z)\right]+KL\left(p(z|x)\right\Vert\boldsymbol{q}(z)\right)\right] 
\end{gathered}
$$

由此，得到了VAE的核心方程。

鉴于上面式子的特点, 我们也许会将$\mathcal{L}$分开为两部分看: $\mathbb{E}_{z\sim p(z|x)}\left[-\ln q(x|z)\right]$的期望和$KL\Bigl(p(z|x)\|q(z)\Bigr)$的期望, 并且认为问题变成了两个loss的分别最小化。

然而这种看法是不妥的,因为$KL\left(p(z|x)\|q(z)\right)=0$意味着$z$没有任何辨识度,所以$-\ln q(x|z)$不可
能小 (预测不准)，而如果$-\ln q(x|z)$小则$q(x|z)$大,预测准确，这时候$p(z|x)$不会太随机，即$KL\left(p(z|x)\|q(z)\right)$不会小，所以这两部分的loss其实是相互拮抗的。所以不能割裂来看, 而是要整体来看。整个的$\mathcal{L}$越小模型就越接近收敛, 而不能只单独观察某一部分的loss。

事实上, 这正是GAN模型中梦寐以求的一一有一个总指标能够指示生成模型的训练进程, 在VAE模型中天然就具备了这种能力了, 而GAN中要到WGAN才有这么一个指标。

现在$q(z),q(x|z),p(z|x)$全都是未知的，连形式都还没确定，而为了实验,就得把式子的每一项都明确写出来。首先,为了便于采样,我们假设$z\sim N(0,I)$,即标准的多元正态分布，这就解决了$q(z)$。那$q(x|z),p(z|x)$呢? 一股脑用神经网络拟合吧。

具体来说, 我们假设$p(z|x)$也是 (各分量独立的) 正态分布, 其均值和方差由$x$来决定,这个“决定”,就是一个神经网络:
$$
p(z|x)=\frac1{\prod_{k=1}^d\sqrt{2\pi\sigma_{(k)}^2(x)}}\exp\left(-\frac12{\left\|\frac{z-\mu(x)}{\sigma(x)}\right\|^2}\right)
$$
这里的$\mu(x),\sigma^2(x)$是输入为$x$、输出分别为均值和方差的神经网络, 其中$\mu(x)$就起到了类似encoder 的作用。既然假定了高斯分布,那么KL散度这一项就可以先算出来:

$$
KL\left(p(z|x)\|q(z)\right)=\frac12\sum_{k=1}^d\left(\mu_{(k)}^2(x)+\sigma_{(k)}^2(x)-\ln\sigma_{(k)}^2(x)-1\right)
$$

也就是我们所说的KL loss, 这个证明已经给出过。

现在只剩生成模型部分$q(x|z)$了，该选什么分布呢？论文[《Auto-Encoding Variational Bayes》](https://arxiv.org/pdf/1312.6114.pdf)给出了两种候选方案：伯努利分布或正态分布。

什么？又是正态分布？是不是太过简化了？然而并没有办法，因为我们要构造一个分布，而不是任意一个函数，既然是分布就得满足归一化的要求，而要满足归一化，又要容易算，我们还真没多少选择。

**伯努利分布**
首先来看伯努利分布, 众所周知它其实就是一个二元分布:

$$
\left.p(\xi)=\left\{\begin{array}{l}\rho,~\xi=1;\\1-\rho,~\xi=0\end{array}\right.\right.
$$
所以伯努利分布只适用于$x$是一个多元的二值向量的情况, 比如$x$是二值图像时 (mnist可以着成是这种情况)。这种情况下,我们用神经网络$\rho(z)$来算参数$\rho$,从而得到

$$
q(x|z)=\prod_{k=1}^D\left(\rho_{(k)}(z)\right)^{x_{(k)}}\left(1-\rho_{(k)}(z)\right)^{1-x_{(k)}}
$$
这时候可以算出

$$
-\ln q(x|z)=\sum_{k=1}^D\left[-x_{(k)}\ln\rho_{(k)}(z)-(1-x_{(k)})\ln\left(1-\rho_{(k)}(z)\right)\right]
$$
 
这表明$\rho(z)$要压缩到0~1之间 (比如用sigmoid激活),然后用交叉熵作为损失函数, 这里$\rho(z)$就起到了类似decoder的作用。

**正态分布模型**
然后是正态分布,这跟$p(z|x)$是一样的,只不过$x,z$交换了位置:
$$
q(x|z)=\frac1{\prod_{k=1}^D\sqrt{2\pi\tilde{\sigma}_{(k)}^2(z)}}\exp\left(-\frac12{\left\|\frac{x-\tilde{\mu}(z)}{\tilde{\sigma}(z)}\right\|^2}\right)
$$
这里的$\tilde{\mu}(z),\tilde{\sigma}^2(z)$是输入为$z$、输出分别为均值和方差的神经网络,$\tilde{\mu}(z)$就起到了decoder的作用。于是

$$
-\ln q(x|z)=\frac12\left\|\frac{x-\tilde{\mu}(z)}{\tilde{\sigma}(z)}\right\|^2+\frac D2\ln2\pi+\frac12\sum_{k=1}^D\ln\tilde{\sigma}_{(k)}^2(z)
$$

很多时候我们会固定方差为一个常数$\tilde{\sigma}^2$, 这时候
$$
-\ln q(x|z)\sim\frac1{2\tilde{\sigma}^2}\left\|x-\tilde{\mu}(z)\right\|^2
$$
这就出现了MSE损失函数。所以现在就清楚了,对于二值数据, 我们可以对d$e$coder用sigmoid函数激活,然后用交叉熵作为损失函数,这对应于$q(x|z)$为伯努利分布;而对于一般数据,我们用MSE作为损失函数,这对应于$q(x|z)$为固定方差的正态分布。

### 重参数化技巧（Reparameterization trick）

对 $z$ 采样是离散的，不可导，但可以通过技巧来将采样过程进行转化，使其可导。
由：
$$
\begin{aligned}
&\frac1{\sqrt{2\pi\sigma^2}}\mathrm{exp}{\left(-\frac{(z-\mu)^2}{2\sigma^2}\right)}dz \\
=& \frac{1}{\sqrt{2\pi}}\mathrm{exp}\biggl[-\frac{1}{2}\biggl(\frac{z-\mu}{\sigma}\biggr)^2\biggr]d\left(\frac{z-\mu}\sigma\biggr)\right.  
\end{aligned} \tag{15}
$$

这说明$(z-\mu)/\sigma=\varepsilon$是服从均值为o、方差为1的标准正态分布的,  从$\mathcal{N}(\mu,\sigma^2)$中采样一个$Z$,相当于从$\mathcal{N}(0,I)$中采样一个$\varepsilon$, 然后让$Z=\mu+\varepsilon\times\sigma$。于是, 我们将从$\mathcal{N}(\mu,\sigma^2)$采样变成了从$\mathcal{N}(0,I)$中采样,然后通过参数变换得到从$N(\mu,\sigma^2)$中采样的结果。这样一来,“采样”这个操作就不用参与梯度下降了,改为采样的结果参与,使得整个模型可训练了。

下面的图展示了这个过程

![VAE](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fdaeef935bd8462b8b4032cc17bcd887~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)


## 思考

### 噪声对模型的泛化性能的影响

假设使用普通的自编码器来进行图像压缩
![[Pasted image 20231018234123.png]]

如上图所示，假设有两张训练图片，一张是全月图，一张是半月图，经过训练我们的自编码器模型已经能无损地还原这两张图片。接下来，我们在code空间上，两张图片的编码点中间处取一点，然后将这一点交给解码器，我们希望新的生成图片是一张清晰的图片（类似3/4全月的样子）。但是，实际的结果是，生成图片是模糊且无法辨认的乱码图。一个比较合理的解释是，因为编码和解码的过程使用了深度神经网络，这是一个非线性的变换过程，所以在code空间上点与点之间的迁移是非常没有规律的。

如何解决这个问题呢？我们可以引入噪声，使得图片的编码区域得到扩大，从而掩盖掉失真的空白编码点。
![[Pasted image 20231018234315.png]]

如上图所示，现在在给两张图片编码的时候加上一点噪音，使得每张图片的编码点出现在绿色箭头所示范围内，于是在训练模型的时候，绿色箭头范围内的点都有可能被采样到，这样解码器在训练时会把绿色范围内的点都尽可能还原成和原图相似的图片。然后我们可以关注之前那个失真点，现在它处于全月图和半月图编码的交界上，于是解码器希望它既要尽量相似于全月图，又要尽量相似于半月图，于是它的还原结果就是两种图的折中（3/4全月图）。

但这只是离散的情况，VAE可以应对连续的情况。但已足以说明噪声的重要性。
### VAE中的噪声与对抗
VAE的本质是什么？VAE虽然也称是AE（AutoEncoder）的一种，但它的做法（或者说它对网络的诠释）是别具一格的。在VAE中，它的Encoder有两个，一个用来计算均值，一个用来计算方差，这已经让人意外了：Encoder不是用来Encode的，是用来算均值和方差的，这真是大新闻了，还有均值和方差不都是统计量吗，怎么是用神经网络来算的？

事实上，我觉得VAE从让普通人望而生畏的变分和贝叶斯理论出发，最后落地到一个具体的模型中，虽然走了比较长的一段路，但最终的模型其实是很接地气的：**它本质上就是在我们常规的自编码器的基础上，对encoder的结果（在VAE中对应着计算均值的网络）加上了“高斯噪声”，使得结果decoder能够对噪声有鲁棒性；而那个额外的KL loss（目的是让均值为0，方差为1），事实上就是相当于对encoder的一个正则项，希望encoder出来的东西均有零均值。**

那另外一个encoder（对应着计算方差的网络）的作用呢？它是用来动态调节噪声的强度的。直觉上来想，**当decoder还没有训练好时（重构误差远大于KL loss），就会适当降低噪声（KL loss增加），使得拟合起来容易一些（重构误差开始下降）；反之，如果decoder训练得还不错时（重构误差小于KL loss），这时候噪声就会增加（KL loss减少），使得拟合更加困难了（重构误差又开始增加），这时候decoder就要想办法提高它的生成能力了。**
![[Pasted image 20231018231830.png]]

说白了，重构的过程是希望没噪声的，而KL loss则希望有高斯噪声的，两者是对立的。所以，VAE跟GAN一样，内部其实是包含了一个对抗的过程，只不过它们两者是混合起来，共同进化的。从这个角度看，VAE的思想似乎还高明一些，因为在GAN中，造假者在进化时，鉴别者是安然不动的，反之亦然。当然，这只是一个侧面，不能说明VAE就比GAN好。GAN真正高明的地方是：它连度量都直接训练出来了，而且这个度量往往比我们人工想的要好（然而GAN本身也有各种问题，这就不展开了）。

从这个讨论中,我们也可以看出,当然,每个$Q(Z|X|)$是不可能完全精确等于标准正态分布, $Q(Z|X)$ 就相当于眼$X$无关了, 重构效果将会极差。最终的结果就会是,$Q(Z|X)$保留了一定的$X$信息, 重构效果也还可以, 并且$P(z)$近似是标准正态分布, 所以同时保留着生成能力。

## VAE实现

