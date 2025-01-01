
## 2021NIPS, Deep Contextual Video Compression

视频编码技术，hevc 等，都是使用预测编码技术。首先预测出当前帧率 $\hat{x}_{t}$，再编码一个差值 $x_{t}-\hat{x}_{t}$.
编码长度是 $H(x_{t}-\hat{x}_{t})$。