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

模型权重地址
## LLaVA-v1.5

[](https://github.com/haotian-liu/LLaVA/blob/main/docs/MODEL_ZOO.md#llava-v15)

| Version   | Size | Schedule   | Checkpoint                                                                              | VQAv2 | GQA  | VizWiz | SQA  | TextVQA | POPE | MME    | MM-Bench | MM-Bench-CN | SEED | LLaVA-Bench-Wild | MM-Vet |
| --------- | ---- | ---------- | --------------------------------------------------------------------------------------- | ----- | ---- | ------ | ---- | ------- | ---- | ------ | -------- | ----------- | ---- | ---------------- | ------ |
| LLaVA-1.5 | 7B   | full_ft-1e | [liuhaotian/llava-v1.5-7b](https://huggingface.co/liuhaotian/llava-v1.5-7b)             | 78.5  | 62.0 | 50.0   | 66.8 | 58.2    | 85.9 | 1510.7 | 64.3     | 58.3        | 58.6 | 65.4             | 31.1   |
| LLaVA-1.5 | 13B  | full_ft-1e | [liuhaotian/llava-v1.5-13b](https://huggingface.co/liuhaotian/llava-v1.5-13b)           | 80.0  | 63.3 | 53.6   | 71.6 | 61.3    | 85.9 | 1531.3 | 67.7     | 63.6        | 61.6 | 72.5             | 36.1   |
| LLaVA-1.5 | 7B   | lora-1e    | [liuhaotian/llava-v1.5-7b-lora](https://huggingface.co/liuhaotian/llava-v1.5-7b-lora)   | 79.1  | 63.0 | 47.8   | 68.4 | 58.2    | 86.4 | 1476.9 | 66.1     | 58.9        | 60.1 | 67.9             | 30.2   |
| LLaVA-1.5 | 13B  | lora-1e    | [liuhaotian/llava-v1.5-13b-lora](https://huggingface.co/liuhaotian/llava-v1.5-13b-lora) | 80.0  | 63.3 | 58.9   | 71.2 | 60.2    | 86.7 | 1541.7 | 68.5     | 61.5        | 61.3 | 69.5             | 38.3   |

在 llava 根目录下，使用 git lfs 下载权重：
```shell
git clone https://huggingface.co/liuhaotian/llava-v1.5-7b
```

但由于网络问题，没下成功。最后是本地下载后，上传服务器。

权重下载完成后，运行的时候会自动下载 CLIP 权重，但由于网络问题，没能下载成功。博客 [保姆级llava-v1.5-7b部署教程\_llava部署-CSDN博客](https://blog.csdn.net/kikiLQQ/article/details/135613642) 给出了解决方案：

1. 下载 CLIP 模型：[Fetching Title#lgdx](https://huggingface.co/openai/clip-vit-large-patch14-336)
2. 上传下载的目录到服务器 llava 根目录下
3. 修改在llava-v1.5-13b/config.json文件，把"mm_vision_tower"修改为刚刚上传的clip-vit-large-patch14-336 目录路径。

## 推理
使用两张卡进行全精度推理：
```shell
CUDA_VISIBLE_DEVICES=0,1 python -m llava.serve.cli --model-path llava-v1.5-13b/ --image-file /home/tew/src/LLaVA/images/llava_v1_5_radar.jpg
```