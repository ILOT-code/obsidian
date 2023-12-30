## 1\. PSNR (Peak Signal-to-Noise Ratio) 峰值信噪比

给定一个大小为$m×n$的干净图像$I$和噪声图像$K$，均方误差$(MSE)$定义为：

$MSE = \frac{1}{mn}\sum_{i=0}^{m-1}\sum_{j=0}^{n-1}[I(i, j)-K(i,j)]^2$

然后$PSNR (dB)$就定义为：

$PSNR = 10 \cdot log_{10}(\frac{MAX_I^2}{MSE})$

其中$MAX_I^2$为图片可能的最大像素值。如果每个像素都由 8 位二进制来表示，那么就为 255。通常，如果像素值由$B$B位二进制来表示，那么$MAX_I = 2^B-1$。

一般地，针对 uint8 数据，最大像素值为 255,；针对浮点型数据，最大像素值为 1。

上面是针对灰度图像的计算方法，如果是彩色图像，通常有三种方法来计算。

-   分别计算 RGB 三个通道的 PSNR，然后取平均值。
-   计算 RGB 三通道的 MSE ，然后再除以 3 。
-   将图片转化为 YCbCr 格式，然后只计算 Y 分量也就是亮度分量的 PSNR。

其中，第二和第三种方法比较常见。

```python
# im1 和 im2 都为灰度图像，uint8 类型

# method 1
diff = im1 - im2
mse = np.mean(np.square(diff))
psnr = 10 * np.log10(255 * 255 / mse)

# method 2
psnr = skimage.measure.compare_psnr(im1, im2, 255)
```

[compare\_psnr(im\_true, im\_test, data\_range=None) 函数原型可见此处](https://link.zhihu.com/?target=http%3A//scikit-image.org/docs/dev/api/skimage.measure.html%23skimage.measure.compare_psnr)

针对超光谱图像，我们需要针对不同波段分别计算 PSNR，然后取平均值，这个指标称为 MPSNR。

## 2\. SSIM (Structural SIMilarity) 结构相似性

$SSIM$公式基于样本$x$和$y$之间的三个比较衡量：亮度 (luminance)、对比度 (contrast) 和结构 (structure)。

$l(x,y) = \frac{2\mu_x \mu_y + c_1}{\mu_x^2+ \mu_y^2 + c_1}$
$c(x,y) = \frac{2\sigma_x \sigma_y + c_2}{\sigma_x^2+ \sigma_y^2 + c_2}$
$s(x,y) = \frac{\sigma_{xy} + c_3}{\sigma_x \sigma_y + c_3}$

一般取$c_3 = c_2 / 2$

-   $\mu_x$为$x$的均值
-   $\mu_y$为$y$的均值
-   $\sigma_x^2$为$x$的方差
-   $\sigma_y^2$为$y$的方差
-   $\sigma_{xy}$为$x$和$y$的协方差
-   $c_1 = (k_1L)^2, c_2 = (k_2L)^2$为两个常数，避免除零
-   $L$为像素值的范围，$2^B-1$
-   $k_1=0.01, k_2=0.03$为默认值

那么

$SSIM(x, y) = [l(x,y)^{\alpha} \cdot c(x,y)^{\beta} \cdot s(x,y)^{\gamma}]$

将$\alpha,\beta,\gamma$设为 1，可以得到

$SSIM(x, y) = \frac{(2\mu_x \mu_y + c_1)(2\sigma_{xy}+c_2)}{(\mu_x^2+ \mu_y^2 + c_1)(\sigma_x^2+\sigma_y^2+c_2)}$
每次计算的时候都从图片上取一个$N×N$的窗口，然后不断滑动窗口进行计算，最后取平均值作为全局的 SSIM。

```python
# im1 和 im2 都为灰度图像，uint8 类型
ssim = skimage.measure.compare_ssim(im1, im2, data_range=255)
```

[compare\_ssim(X, Y, win\_size=None, gradient=False, data\_range=None, multichannel=False, gaussian\_weights=False, full=False, \*\*kwargs) 函数原型可见此处](https://link.zhihu.com/?target=http%3A//scikit-image.org/docs/dev/api/skimage.measure.html%23skimage.measure.compare_ssim)

针对超光谱图像，我们需要针对不同波段分别计算 SSIM，然后取平均值，这个指标称为 MSSIM。