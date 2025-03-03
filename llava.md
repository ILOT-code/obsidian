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
这部分代码在目录 `multimodal_encoder` 下面。只有两个文件，通过 `build_vision_tower` 函数来构建图像 encoder。有两种 encoder：`CLIPV`