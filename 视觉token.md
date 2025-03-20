
系统提示词、图像、用户问题、已生成的回答这些 token 被一股脑丢给 LLM 来处理。在 LLaVA 模型中，图像 token 的占比非常高。直观的感受是，图像 token 的数量肯定是冗余的，人类在看一张图片时，只会根据问题来专注某一小部分区域，或是专注于图像的某个特点。对视觉 token 的压缩有很大潜力。
下面我总结了一些论文中的方法以及他们发现的现象。

## attention 的不平衡
@FastV
测试在解码输出的 token 过程中，各类型的 token 获得的 attention 之和以及效率（也就是 attention 的和除以该 token 的数量）。
![[Pasted image 20250320140603.png]]
image tokens 数量占绝大部分。在浅层中，attention 的分布还较为均衡，而在 deep layerh 中，分布局部均衡，image token 的效率也极低，attention 几乎都聚集在 sys tokenh 上。
(这种现象我认为并不能完全说明 image token 的冗余，因为 deep layer 的输入的 token 序列本来就是 shallow layer 处理来的，而 shallow layer 中 image token 占据了很大一部分 attention)

@DyRate
下面的图展示了在逐 tokenh 输出的过程中，各类型 token 的 attention 占比。说明随着输出序列的增加，img token 的 attention 占比也在减少。
![[Pasted image 20250320144315.png]]

@VisionZip
下面的图展示了，即使在 image token 中，不同位置的 image token attention 差异也很大。而且这些 attention 大的位置也是非常奇怪，往往并不聚焦与图像中在视觉上关键的区域（比如说这个人和车）
![[Pasted image 20250320160726.png]]

## FastV
在第 $K$ 层，对 token 的重要性(attention mask 的列向求和)进行排序，并丢弃最后的 $R\%$ 的token。（这些被丢弃的 token 在随后的层中也不存在）
![[Pasted image 20250320143327.png]] 
## DyRate
generation 过程中，image token 的 attention 占比逐渐减少，因此，需要设计一种动态的 token 削减策略，在不同的 generation steps以不同的比例来剪去 image token.
![[Pasted image 20250320152556.png]]

大体思路就是，在当前 generation step 中，取出当前预测 token 的 attention 向量，训练一个分类器，依次向量为输入，输出各种剪枝率（剪刀大小）的概率。然后选择一个剪枝率去按照某种策略剪去 tokens。
![[Pasted image 20250320152654.png]]
上面的过程在 generation 过程是说得通的，但在 training 过程中需要客服采样的问题。
具体而言，定义了 $K$ 个不同的剪枝率: $\mathcal{R}=P\{r_{k}\}_{k=1}^{K},r_{k}=\frac{k-1}{K}$
利用分类器从 attentionn 向量中得到各个剪枝率的概率：
$$
\pi_R=Softmax(C_\theta(\mathbf{v}_t))=\{P(r_k)\}_{k=1}^K,
$$
按照某种重要性排序策略，就能为 $N$ 个 token 设定一个掩码：
$$
m_i^k=\begin{cases}1&\text{if }i\leq\frac{N}{K}(k-1),\\0&\text{otherwise,}\end{cases}
$$
训练过程中，使用 Gumbel-Softmax 来进行采样，生成一个掩码：
$$
m=\sum_{k=1}^K\text{Gumbel-Softmax}(\pi_R)_{*,k}\cdot m^k,
$$
$\text{Gumbel-Softmax}(\pi_R)$ 会生成一个长度是 $K$ 的向量。
> 这个式子更像一个加权求和的过程，既让如此，为何不直接用已知的 $\pi_{R}$ 来进行加权呢？

利用这个掩码，为 attention mask 增加一个 mash $M \in {0,1}^{N\times N}$:
$$
M_{i,j}=\begin{cases}1&\quad i=j,\\m_i&\quad i\neq j.\end{cases}
$$

## LLaMA-VID
利用用户输入的文本信息来与 visual encoder 输出的 token 作一次 cross-attentiomn, 生成一个和用户提问相关的 image token $E_{t}^{T}\in R^{1\times C}$
 visual encoder 的输出也会经过 pool 后生成长度很短的 tokens $E_{t}^{V}\in R^{{l\times C}}$
![[Pasted image 20250320155411.png]]
## VisionZip
同样利用 attention score 来进行排序，和 FastV 一样。不同的是，如果使用的 visual encodr 有 cls 这样的 token 的话，那就直接看 cls token 上的 attention 分布。

首先选择出分数最高的几个 dominant token。对剩下的 token, 使用类似聚类的方法进行 merge, token 间距离的计算使用 key 向量的余弦相似度
![[Pasted image 20250320162330.png]]