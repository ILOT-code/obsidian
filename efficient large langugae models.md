
本文把研究分为三个方向：
1. Model-Centric: 算法级别和系统级别，是主要的关注方向。
2. Data-Centric：以数据为中心的方法，聚焦于数据质量和结构在提升 LLMs 效率中的作用。
3. LLM Frameworks：为 LLM 的训练、微调、推理专门设计的框架，相比 Pytorch, TensorFlow 更具专业性。

# Model Centric
分为五个类别：
1. model compression：关注如何减少规模和计算量
2. effcient pre-training：高效的预训练
3. effcient fine-tuning：高效微调
4. effcient inference：高效推理
5. effcient architecture：对 attention, FFN 等层的改进

## Model Compression
![[Pasted image 20250324130821.png]]

![[Pasted image 20250324195601.png]]
### Quantization
量化就是把高精度的模型权重或者 activationsh 转化成低精度的形式：
$$
\mathbf{X}^\mathrm{L}=\mathrm{Round}\left(\frac{\mathrm{absmax}\left(\mathbf{X}^\mathrm{L}\right)}{\mathrm{absmax}\left(\mathbf{X}^\mathrm{H}\right)}\mathbf{X}^\mathrm{H}\right)=\mathrm{Round}\left(\mathcal{K}\cdot\mathbf{X}^\mathrm{H}\right),
$$
量化可以在模型训练完毕后进行 PTQ，也可以在训练时就进行 QAT，但LLM 的量化多采用 PTQ 的形式，能减少训练时的开销。
与其他 LLM 压缩方法（如剪枝和低秩近似）相比，量化方法已被证明在压缩与精度的权衡上表现更优（Li 等，2024）

#### PTQ
由于 PTQ 是训练无法感知的，需要对量化引起的损失进行额外的弥补，因此会有一个小的 calibration dataset 去更新量化后的参数或者 activations。.


**weight-only**: 
只量化权重，推理时反量化。
salient weight： 存在一小部分模型权重，其激活幅度较大，在决定量化损失中起着关键作用。对 salient weight 直接量化会带来显著的影响。
activation outliers：某些激活值，其幅度也很大，它们对于的矩阵权重如果量化，也会带来显著影响。

LLM. int (8)：采用向量量化的形式，量化到 8 bits
GPTQ: 利用逆 Hessian 矩阵信息，把权重量化到 3 or 4 bits, 能够在 4 gpu hours 上量化 175 B 的模型。

很多改进版本的方法对这类 salient weight 或是和 activation outliers 对应的权重采用 fp16, 不进行量化，对剩余部分进行量化。这类权重的分布有一定规律，他们往往集中在少数的几个 channel 上，带来了很大便利。

FineQuant 设计了一种经验式的方法，为不同的权重分配不同的量化粒度，同样能克服 outliers 的问题。

**weight-activation Co-Quantization**
ZeroQuant: group-wise 的形式量化矩阵权重，token-wise 的形式量化 activations. 后面一些方法在此基础上引入了低秩矩阵近似来减少精度下降问题。
SmoothQuant 对 activations 进行逐通道缩放，使得 activation outliers 和普通的 activations 变得差不多大，然后对矩阵权重进行量化。
OliVe 直接把 outliers 临近的维度剪去，节省下的比特为 outliers 提供更高精度。
RPTQ 按通道对 activations 分组，具有相似范围的通道聚合为一组，使用一样的量化粒度。
 Outlier Suppression+观察到，激活异常值具有不对称性，并且倾向于集中在特定通道中。基于这一观察，为个别通道引入平移和缩放操作，以中和那些异常值

#### QAT
QAT 在训练过程中对 LLMs 进行量化，使 LLMs 能够学习到量化带来的影响从而进行调整。由于 QAT 需要使用完整的训练数据集进行训练，因此其成本和时间消耗远高于 PTQ。

QuantGPT 利用全精度教师模型对量化的学生模型进行蒸馏训练。QuantGPT 在 GPT-2 和 BART 上分别实现了 14.4 倍和 13.4 倍的压缩率，且性能与全精度模型相当。
LLM-QAT 同样也是蒸馏训练，但除了量化权重和激活值外，还量化了 KV-cache.
BitNet 提出了了 1-bit 的 QAT 方法。在训练过程中使用低精度二值权重和量化激活值，同时保持优化器状态和梯度的高精度。

### Parameter Pruning
剪去冗余或不重要的参数。

#### structured pruning
结构性剪枝指的是剪去连续的参数组或结构，如矩阵的行、列。这种剪枝可以提供推理速度。
LLM-Pruner 利用梯度信息来剪去一些结构，然后用 LoRA 微调。
Sheared LLaMA 在 LLM-PRuner 的基础上提出两点改进。1：通过端到端的方式去除层、头、中间层和隐藏层维度，将较大模型剪枝至指定目标形状。2,动态批次加载，基于各领域损失动态配置每个训练批次中采样数据的组成。
LoRAPrune提出了一种基于 LoRA 的剪枝准则，利用 LoRA 的权重和梯度进行重要性估计。迭代地消除多余的通道和头。

#### Unstructured Pruning
非结构化剪枝灵活性更大，精度下降较小。但稀疏化是不规则的，难以进行加速。
SparseGPT 将剪枝问题表述为稀疏回归问题，并通过基于逆 Hessian 矩阵的近似求解器来解决。
Wanda 基于权重幅值与其相应输入激活的乘积值进行权重剪枝，它既不依赖二阶信息，也不需要进行权重更新。

### Low-Rank Approximation
对权重矩阵作近似，例如： $\mathbf{W}\approx\mathbf{U}\mathbf{V}^\top$, $U,V$ 矩阵要小很多。
TensorGPT 使用 Tensor-Train Decomposition 来处理 embedding layers。创建了一种更高效的嵌入方式。
FWSVD 使用低秩近似来处理权重矩阵，而不仅是 embedding。他在数据集上，计算权重的重要性，以重要性为衡量设计了一种加权形式的 SVD 算法。
ASVD 这是 training-free 的，它根据激活值的分布来缩放权重矩阵，来减小误差。
SVD-LLM 建立了奇异值和损失函数的联系，因此可以选择性的、和 loss 相关的来截断奇异值，因此具有更好的性能。

### Knowledge Distillation
让相对小的学生模型学习大的教师模型的输出。分为白盒和黑盒蒸馏，黑盒只能看到最终的输出，百盒可以看到教师模型各层的状态。

#### White-Box
BabyLLaMA 最先作出 s 场所，使用 FPT-2 和一系列 LLaMA 的集成模型训练一个小的 LLaMA 模型，取得了很好的效果。
MiniLLM 提出，反向 KL 熵($KL(Q_{\theta}||P)$)更适合 LLM 任务。
TED 对齐教师与学生的每一层，而不仅仅是输出层。
GKD通过在训练过程中从学生模型中提取输出序列来解决分布不匹配的问题，GKD 可以与其他蒸馏方法结合使用，以提高其压缩性能。

#### Black-Box
MultitaskICT 设计了 in-context learning 技术，把教师的新任务学习能力带给学生模型。
Lion 提出了一种对抗蒸馏架构，它促使 LLMs 识别具有挑战性的指令，并为学生模型生成新的复杂指令，从而建立一个包含模仿、判别和生成的三阶段对抗循环。

另一条路径，是利用教师模型的 CoT 来指导学生模型。
Fu 等人，利用教师模型的思维链，构建出指令集合，来训练学生模型。
Fine-tune-CoT利用现有的零样本 CoT 提示技术从大语言模型生成推理过程，用它们微调较小的学生模型。此外，该方法引入了多样化推理，通过随机采样从教师模型中生成多种推理解决方案，从而丰富学生模型的训练数据。
SOCRATIC CoT将原始问题分解为一系列较小的子问题，并利用这种分解来指导推理的中间步骤。它用于训练一对较小的蒸馏模型：一个专门负责分析问题，另一个则专注于解决这些子问题。
SCOTT利用 LLM 生成的推理，在反事实推理框架下训练学生模型。SCOTT 生成的 CoT 推理依据比原始 CoT 提示更具忠实性。

## Efficient Pre-Training
预训练大语言模型消耗巨大成本，高效的预训练技术 i 旨在减少其成本。
![[Pasted image 20250326185136.png]]
### Mixed Precision Training
混合精度训练通过使用低精度模型进行前向和反向传播，然后将计算出的低精度梯度转换为高精度以更新原始高精度权重，从而提高了预训练效率。
AMP 保持全精度（FP 32）权重的副本用于更新，而权重、激活和梯度则以 FP 16 存储用于算术运算。
AMP 中的 FP 16 由于数值范围受限会导致精度损失。为了解决这一问题，具有更大动态范围（指数位数比 fp 16更多）的 Brain Floating Point（BFLOAT 16）被提出。
### Scaling Models
通过把较小模型的权重扩展到更大的模型，来加速预训练过程。
progressive stacking: 简单来说，就是先训练一个n层的模型，然后把训练好的参数复制多次，填满一个2n层的模型（宽度相同），再进行继续预训练。以此类推，可以较快得到层数2n、4n、8n等的模型。
FPI: 通过在深度和宽度上扩展，使得大模型的输入和小模型完全一致。深度上可以加identity map.
Yang观察到，progressive stacking随着深度加深，训练速度却有所下降。为解决这一问题，他们提出了多阶段层训练（MSLT），该方法仅更新输出层和新引入的顶层编码器层，而保持之前训练好的层不变。一旦所有层都完成训练。
AKI: 一种增加宽度的做法：
![[Pasted image 20250326180428.png]]
CompoundGrow，该方法首先训练一个小模型，然后通过增加输入长度、模型宽度和深度来逐步扩展模型。

### Initialization Techniques
初始化可以加速模型的收敛。
Fixup和ZerO将主干网络初始化为零，以保持信号的可辨别性。
SkipInit用零值乘法器替代了BN
ReZero通过添加零值参数来保持一致性，从而实现更快的收敛。

### Training Optimizers
在大模型训练中，Adam和AdamW使用广泛，但他们内存和算力的消耗很大。
Lion 通过搜索和人工干预，找到了 Lion 优化器，更省显存和算力。
Sophia 是一个轻量的二阶的优化器，通过估计的 Hessian 矩阵来更新。
### System-Level Pre-Training Efficiency Optimization
一些 DP 和 DDP 策略

## Efficient Fine-Tuning
分为 parameter-efficient fine tuning 和 memory efficient

### parameter-efficient
#### Low-Rank Adaptation
LoRA：通过两个低秩矩阵 $\mathbf{A}\in \mathbb{R}^{m\times r}\mathbf{B}\in \mathbb{R}^{r\times n}$ ，来优化矩阵：
$$
\mathbf{W}\leftarrow\mathbf{W}+\Delta\mathbf{W}=\mathbf{W}+\mathbf{A}\cdot\mathbf{B}.
$$
只有 AB 被优化，原始矩阵不变，大大减少了需要优化的参数量。

LoRA-FA: 把 A 矩阵固定，只训练 B 矩阵。这样，XA 就会把 X 降维到一个小的空间，所需存储空间就会变少。
LongLoRA将 LoRA 扩展到了长上下文微调场景中。它引入了移位短注意力，有效促进了上下文扩展。
Zhang 根据权重矩阵的重要性分数，动态的为他们分配不同大小的 AB 矩阵。

#### Adapter-based Tuning
Adapters 是一个瓶颈层，是可训练的模块，被嵌入到模型中去，先对输入进行降维，再升维。
基于适配器的调优包括串联适配器和并联适配器。在串联适配器中，每个 LLM 层的注意力和前馈模块后添加了两个适配器模块；而并联适配器则在 LLM 每一层的注意力和前馈模块旁放置了两个适配器模块。