****

class  CLIPVisionTransformer:
输入： pixel, (N, C, H, W)
输出：last_hidden_state (N, seq_len, embed_Dim), pooled_output (N, 0 ,embed_dim)(对 cls token 池化的结果)，以及中间 state 的列表，和一个 attention 权重的列表
先对图像进行嵌入：
class CLIPVisionEmbedings
输入：pixel, (N, C, H, W)
输出：tokens, (N, num_positions, embed_dim)
描述： 使用 ViT 的嵌入方法，把图像装化成 Token, 并为每个位置设计了一个可学习的位置编码，并在 token 序列首部插入 cls token.

在对该嵌入进行 encode:
class CLIPEncoder:
输入: (N, seq_len, embed_dim), 两个关于 attention 的 mask attention_mask, 
输出：list or dict, last_hidden_state(N, seq_len, embed_dim), 以及中间 state 的列表，和一个 attention 权重的列表
描述：这个类有一系列相同的 class CLIPEncoderLayer 构成
	Class CLIPEncoderLayer
	输入： (N, seq_len, embed_dim)，两个关于 attention 的 mask attention_mask, causal_attention_mask
	输出：list,（N, seq_len, embed_dim）以及 atten 权重矩阵 (N\*num_hed, seq_len, seq_len)




# 图像编码部分
这部分代码在目录 `multimodal_encoder` 下面。只有两个文件，通过 `build_vision_tower` 函数来构建图像 encoder。有两种 encoder：`CLIPVisionTower` 和 `CLIPVisionTowerS2`，它们是类继承的关系，后者再前者的基础上加上了llava 1.5 提出的图像多尺度分辨率特征方法。

`CLIPVisionTower` 主要有三个重要参数:
1. `self.vision_tower_name`，利用 trasnformer 库，直接生成一个 CLIP 模型。
2. `self.select_layer`，CLIP 模型是许多个 Transformer 构成，这个参数定义了到底选择那个中间 state 来输出。
3. `self.select_feature`，ViT 会向 token 序列的首部添加一个 cls token, 这个参数定义了是否选择这个特殊的首 token。

`CLIPVisionTower` 类 `forward` 函数的输入与输出：
1. 输入，一系列一张图片，图片的形状(b, c, h, w)
2. 输出,，一系列或一张 feature，形状 (b, seq_len, embed_dim)

