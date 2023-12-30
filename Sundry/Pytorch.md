## scatter

`target.scatter(dim, index, src)`

将 `src` 这个张量，按照 `index` 指定的坐标，沿着 `dim` 给定的方向，进行播撒，以占领 `target` 中的一些点位。

src, taeget, index 必须有相同的维度。并且 index 中的值小于 target. size (dim)，也就是指定的坐标值不能超了。
一般而言，index 和 src 大小相等，但 index 是可以小于 src 的。

```python
target[index[i][j][k]][j][k] = src[i][j][k]  # if dim == 0
target[i][index[i][j][k]][k] = src[i][j][k]  # if dim == 1
target[i][j][index[i][j][k]] = src[i][j][k]  # if dim == 2
```
对所有维度而言, `index.size(d) <= src.size(d)` , 并且 `index.size(d) <= self.size(d)` 对于 `d != dim`. Note that `index` and `src` do not broadcast.

`scatter` 不会将 target 改变，而 `scatter_` 会。一般带 `_` 的函数都会这样。

## unsqueeze

`torch.unsqueeze(input, dim, out=None)`

返回张量与输入张量共享内存，所以改变其中一个的内容会改变另一个

将 `input` 在指定的维度上加一个维度。

如果 `input.shape=(2,3,4)`, `dim=1`，则输出维度 (2,1,3,4)。
如果 dim 是负数，插入的维度是 imput. dim ()+dim+1。

同样，有 `unsqueeze_`。

## squeeze

`torch.squeeze(input, dim=None, out=None)`

返回张量与输入张量共享内存，所以改变其中一个的内容会改变另一个

将输入张量形状中的1 去除并返回。 如果输入是形如(A×1×B×1×C×1×D)，那么输出形状就为： (A×B×C×D)

当给定 dim 时，那么挤压操作只在给定维度上。例如，输入形状为: (A×1×B), `squeeze(input, 0)` 将会保持张量不变，只有用 `squeeze(input, 1)`，形状会变成 (A×B)。

## matul
`torch.matul(a,b)`
[PyTorch 中 torch.matmul() 函数的文档详解 - Lowell\_liu - 博客园](https://www.cnblogs.com/HOMEofLowell/p/15963140.html)

矩阵乘法。
有 5 种情况：一维 × 一维、二维 × 二维、一维 × 二维、二维 × 一维、涉及到三维及三维以上维度的张量的乘法。

1. 如果两个张量都是一维的，即 `torch.Size([n])` ，此时返回两个向量的点积。作用与 `torch.dot()` 相同
2. 如果两个参数都是二维张量，那么将返回矩阵乘积。作用与 `torch.mm()` 相同
3. 如果第一个参数是一维张量，第二个参数是二维张量，那么在一维张量的前面增加一个维度，然后进行矩阵乘法，矩阵乘法结束后移除添加的维度。
4. 如果第一个参数是二维张量（矩阵），第二个参数是一维张量（向量），那么将返回矩阵×向量的积。作用与 `torch.mv()` 相同。另外要求矩阵的形状和向量的形状满足矩阵乘法的要求。
5. 如果两个参数均至少为一维，且其中一个参数的 `ndim > 2`，那么……（一番处理），然后进行批量矩阵乘法。这条规则将所有涉及到三维张量及三维以上的张量（下文称为高维张量）的乘法分为三类：一维张量 × 高维张量、高维张量 × 一维张量、二维及二维以上的张量 × 二维及二维以上的张量。
   1. 如果第一个参数是一维张量，那么在此张量**之前**增加一个维度。
   2. 如果第二个参数是一维张量，那么在此张量**之后**增加一个维度。
   3. 由于上述两个规则，**所有涉及到一维张量和高维张量的乘法都被转变为二维及二维以上的张量 × 二维及二维以上的张量。** 然后除掉最右边的两个维度，对剩下的维度进行广播，然后就可以进行批量矩阵乘法。

   
## view
`target.view(a,b,c..)`
和 `reshape` 一样，当参数列表只有一个 `-1` 时，展开成一维。

## cat
`torch.cat((A,B),dim)`

将 A，B 按照指定的维度拼接。除 dim 外，其它维度大小相等。

如果 A.shape=(4,3,3), B.shape=(4,2,3)，dim=1，返回的 shape 是 (4,5,3)。其中 `target[i,0:3,j]=A[i,0:3,j]`, `target[i,3:5,j]=B[i,3:5,j]`

如果 `dim` 参数的值为 `None`，则会将所有输入张量展开成一维。

## full_like
`torch.full_like(_input_, _fill_value_)`

返回一个张量，和 `_input_` 形状一样，值全是 `_fill_value_`。

## index_select

`torch.index_select(_input_, _dim_, _index_, _out=None_)`
函数返回的是沿着输入张量的指定维度的指定索引号进行索引的张量子集。
index(LongTensor)，是包含索引号的 1D 张量
输出与输入同维度数，并且除了指定的 维度 dim，其它维度大小相等，输出张量在 dim 维度上的大小等于 index 的长度。