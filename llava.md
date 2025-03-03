****

class  CLIPVisionTransformer:
输入： pixel, (N, C, H, W)

先对图像进行嵌入：
class CLIPVisionEmbedings
输入：pixel, (N, C, H, W)
输出：tokens, (N, num_positions, embed_dim)
描述： 使用 ViT 的嵌入方法，把图像装化成 Token, 并为每个位置设计了一个可学习的位置编码。

