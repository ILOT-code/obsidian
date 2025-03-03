****
# 图像编码部分
这部分代码在目录 `multimodal_encoder` 下面。只有两个文件，通过 `build_vision_tower` 函数来构建图像 encoder。有两种 encoder：`CLIPVisionTower` 和 `CLIPVisionTowerS2`，它们是类继承的关系，后者再前者的基础上加上了llava 1.5 提出的图像多尺度分辨率特征方法。

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

