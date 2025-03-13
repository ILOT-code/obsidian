****

llava mini

# 图像编码部分
这部分代码在目录 `multimodal_encoder` 下面。只有两个文件，通过 `build_vision_tower` 函数来构建图像 encoder。有两种 encoder：`CLIPVisionTower` 和 `CLIPVisionTowerS2`，它们是类继承的关系，后者在前者的基础上加上了llava 1.5 提出的图像多尺度分辨率特征方法，但他们输入输出的格式与形状是一样的。

## encoder
`CLIPVisionTower` 主要有三个重要参数:
1. `self.vision_tower_name`，利用 trasnformer 库，直接生成一个 CLIP 模型。
2. `self.select_layer`，CLIP 模型是许多个 Transformer 构成，这个参数定义了到底选择那个中间 state 来输出。
3. `self.select_feature`，ViT 会向 token 序列的首部添加一个 cls token, 这个参数定义了是否选择这个特殊的首 token。

`CLIPVisionTower` 类 `forward` 函数的输入与输出：
1. 输入，一系列一张图片，图片的形状(b, c, h, w)
2. 输出,，一系列或一张 feature，形状 (b, seq_len, embed_dim)

openai `CLIP` 的核心实现在类 `CLIPVisionTransformer` 中，他首先使用类 `CLIPVisionEmbedings` 来把图像转化成 token 序列(这是通过一个 conv 2d 实现的)，并在 tokene 序列首部插入一个可学习的 cls token，并为所有位置设计一个可学习的位置编码。
然后通过类 `CLIPEncoder` 来进行处理，这个类是一连串的 Transformer 和 mlp 构成。最后的返回结果包括 last_hidden_state, 一个包含个 hidden_state 的列表，一个包含各个 attention_weight 的列表。当然 llavad 关注的只有这个 hidden_state.

## 多分辨率特征融合
这通过 `multiscale_forward` 函数实现。其输入参数主要有：
1. model：模型本身
2. input, 输入的图片 (需要 h\==w)
3. img_sizes, 一个列表，input 会被缩放到这些大小
4. max_split_size, 分割的尺寸
下面的图描绘了函数的处理过程，忽略了一些细节处理 (比如说对 cls token 的处理)
![[Drawing 2025-03-03 19.51.02.excalidraw]] 





# llama
llava 调用了transformer 中 llama 的实现。这部分代码集中在 `transformers/model/llama/modeling_llama.py` 中。

## llama 的自定义层
首先是 `LlamaRMSNorm`，它和一般的 LayerNorm 有点区别，假设 $x$ 是一个 $d$ 维向量，$w$ 是可学习的 $d$ 维向量
$$
x_{out} =\frac{x}{\sqrt{ \frac{1}{d}\ \sum_{i=1}^{d} x_{i}^{2} }} \times w 
$$


然后是旋转位置编码层，`LlamaRotaryEmbedding`，它使用在 query 和 key 向量上。它有两种变体：`LlamaLinearScalingRotaryEmbedding`. `LlamaDynamicNTKScalingRotaryEmbedding`，区别在于是否对角度进行缩放。这个层仅仅是得到一个嵌入，需要通过 `apply_rotary_pos_emb` 函数把 q 和 k 向量真正进行位置编码。

`LlamaMLP` 这个层的实现