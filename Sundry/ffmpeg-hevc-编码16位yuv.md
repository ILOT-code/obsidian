[ffmpeg - Convert 16bit Grayscale PNG to HEVC/x265 - Stack Overflow](https://stackoverflow.com/questions/66155414/convert-16bit-grayscale-png-to-hevc-x265)


在 ffmpeg 中，似乎并不支持高位的位深度，在编码器内部最多只支持 12 位来存储像素值。而在 ffv1 以及 hm 中，在编译过程中，是可以选择支持 16 位的。

实际上，我发现数据集的范围是\[-2048,2048\]，也就是 12 位，将原始数据加上 2048 后可以规整到\[0,4096\]。在保存为 png 时需要注意，ffmpeg 只会读取高位的 12 位数据，并且在显示的时候是只看高 12 位的，也就是说，在存储为 png 时，原始数据需要左移 4。


另外，在这种情况下，用满了 12 位的深度，ffmpeg-hevc 的无损编码仍旧有一定损失。
```bash
 ffmpeg -s 512*512 -pix_fmt gray16le -r 1 -i input.yuv -c:v libx265 -x265-params lossless=1 output.mp4
 ffmpeg -i output.mp4 -pix_fmt gray16le output.yuv
```

```python
    path1 = "ffmpeg/study_1064.yuv"  # 原始yuv
    path2 = "ffmpeg/output.yuv"       #重建yuv
    img1 = np.fromfile(path1, dtype=np.uint16)
    img2 = np.fromfile(path2, dtype=np.uint16)
  
    img1 = img1 >> 4
    img2 = img2 >> 4

    ind = img1 != img2
    img = np.vstack((img1, img2))
    print(img[:, ind])
```

得到的结果是：
![[Pasted image 20231203150812.png]]

差距是 1，为了验证差距是 1：
```python
img2[img1 != img2] += 1
print(np.array_equal(img1, img2))
```

得到的结果是 True。

我怀疑这是因为内部运算造成的误差，如果有可能将 ffmpeg 的内部像素深度改成 16，这是可以解决的。

为了验证这个猜想，我构造了 8 位的灰度 yuv，并指定 -pix_fmt gray8 这种情况下，ffmpeg 的无损编码可以正确执行。