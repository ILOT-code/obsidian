
## pgmpy

这是有关贝叶斯的包，直接 `conda install pgmpy` 无法无法安装。官网给了安装指导。

[Installation — pgmpy 0.1.23 documentation](https://pgmpy.org/started/install.html)

## pictest 环境搭建

其中主要是 opencv 的安装，出现了奇怪的问题，在 py 文件中，无法识别 cv2，但在 ipynb 文件中却可以正常运行，十分奇怪。

### opencv

```
conda install -c https://conda.anaconda.org/menpo opencv3 #安装opencv3（python3.9环境下无法安装）
conda install -c https://conda.anaconda.org/menpo opencv #安装最新版opencv4 
```

### tqdm
```
conda install tqdm
```
### nibabel
```
conda install -c conda-forge nibabel
```
### imageio
```
conda install imageio
```