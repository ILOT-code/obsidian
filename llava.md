****

class  CLIPVisionTransformer:
输入： pixel, (N, C, H, W)

先对图像进行嵌入：
class CLIPVisionEmbedings
输入：pixel, (N, C, H, W)
输出：tokens, (N,)
使用 ViT 的嵌入方法，把图像装化成 Token, 

