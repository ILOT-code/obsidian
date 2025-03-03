****

class  CLIPVisionTransformer:
输入： pixel, (N, C, H, W)

先对图像进行嵌入：
class CLIPVisionEmbedings
输入：pixel, (N, C, H, W)
输出：tokens, (N, num_positions, embed_dim)
描述： 使用 ViT 的嵌入方法，把图像装化成 Token, 并为每个位置设计了一个可学习的位置编码。

在对该嵌入进行 encode:
class CLIPEncoder:
描述：这个类有一系列相同的 class CLIPEncoderLayer 构成
Class CLIPEncoderL

