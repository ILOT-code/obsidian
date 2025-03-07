****
# 代码与环境安装
下载代码：
```shell
git clone https://github.com/haotian-liu/LLaVA.git
cd LLaVA
```

安装虚拟环境

```shell
conda create -n llava python=3.10 -y
conda activate llava
pip install --upgrade pip  # enable PEP 660 support
pip install -e .
```

要训练微调的话还需安装(部署不用)：
```shell
pip install -e ".[train]"
pip install flash-attn --no-build-isolation
```

# 权重下载
