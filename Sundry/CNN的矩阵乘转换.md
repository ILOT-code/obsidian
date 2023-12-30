
CNN 可以写成矩阵乘的形式，下面给出过程和实现。

## 定义
- $c_1,c_2$：输入图像的通道数和输出通道数
- $k_1,k_2$：卷积核的高和宽
- $h,w$：输出图像的高和宽
- $s,d$：步长和核之间距离（对应扩张卷积）

## 方法
![[Pasted image 20231026165709.png]]

$W$ 是 $c_{2} \times  c_1k_1k_2$ 的大小，每一行代表着展开的卷积，按照：
![[Pasted image 20231026165921.png]]
这个方式展开。
$X$ 的形状是 $c_1k_1k_{2}\times hw$ 。每一列对应着一次卷积操作的那些点，卷积核移动 $hw$ 次，对应着每一列。

## 编程实现

首先来看 `W`，这是比较简单的。
```python
    W：卷积层的卷积核参数，为 (kernel_rows, kernel_cols, in_ch, out_ch)
    W_col = W.transpose(3, 2, 0, 1).reshape(out_ch, -1)
```
按照此顺序的 reshape 将得到上面展示的展开结果。

下面来看 `X`。

我们现在只考虑对单个 sample。重点在于得到这张 $c_1k_1k_{2}\times hw$ 的矩阵，每一个点的的 输入通道是几，行列是几。应用上面的方法，这当然可以得到，但可能实现比较复杂。这里看到一份不用循环就实现的代码。

我们先来看第一个，也就是输入通道是几。对于这张 $c_1k_1k_{2}\times hw$ 的矩阵，同一行的点，它的输入通道是一样的。因为它对应卷积核里的同一个点。其次，对于在同一列中的方向，它是重复 $k_1k_2$ 次后，再转换通道。那么 可以得到：
```python
	k = np.repeat(np.arange(n_in), fr * fc).reshape(-1, 1)
```
它是列向量，最后会广播成 $c_1k_1k_{2}\times hw$ 的矩阵，每一个点的值对应了该点的通道数是几。

再来看每个点对应的行是几。不妨先分析最左边那一列，就是 patch 1。它是先不变，经过 `k_2` 列后，再加 1，重复直到结束一个卷积核，然后接着展开下一个通道对应的卷积核，这是对前面结果的重复（行的变化是一致的）。如果存在 $d$，那么每一个点的行值需要乘以 $d$， 那么这一行就可以写为：
```python
    i0 = np.repeat(np.arange(fr), fc)
    i0 = np.tile(i0, n_in) * d
```
它又是怎么样随着列的变换而变换呢？想象一下，第二列是卷积核左移 `s` 得到的，接着左移。直到移到 $w$ 次后，就换行（行数改变），接着重复。每一次移动，卷积核上所有的对应点的行值变化是相等的。那么每一列相对第一列的附加变换可以写为：
```python
    i1 = s * np.repeat(np.arange(out_rows), out_cols)
```
然后，这个 $c_1 k_1 k_{2}\times h$ 的矩阵上每一个点的行值可以写为：
```python
	i = i0.reshape(-1, 1) + i1.reshape(1, -1)
```
这使用了广播机制来完成加法。

对于每个点的列是几，使用和上面一样的分析，得到：
```python
    j0 = np.tile(np.arange(fc), fr * n_in) * d
    j1 = s * np.tile(np.arange(out_cols), out_rows)
    j = j0.reshape(-1, 1) + j1.reshape(1, -1)
```

自此，我们得到了装欢后的这张 $c_1 k_1 k_{2}\times h$ 矩阵上每一个点的通道，行，列。也就能确定它了。

下面来看最后怎么根据 k, i，j 来得到这张举证。
```python
	## X_pad：填充后的输入数组，为 (n_samples, in_rows, in_cols, in_ch)
    k, i, j = _im2col_indices((n_samp, n_in, in_rows, in_cols), fr, fc, p, s, d)
    # X_col.shape = (n_samples, kernel_rows*kernel_cols*n_in, out_rows*out_cols)
    X_col = X_pad[:, k, i, j]
    X_col = X_col.transpose(1, 2, 0).reshape(fr * fc * n_in, -1)
```

首先，得到 k, i, j。然后直接在原矩阵上索引就可以了。
对于 k, i, j 组成的索引，首先 k 被广播，然后对于每一个 sample，它们索引出了这个 sample 对应的矩阵。