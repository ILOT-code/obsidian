[解决Win10 PowerShell无法激活Anaconda环境的问题 - Dereen - 博客园](https://www.cnblogs.com/dereen/p/ps_conda_env.html)

在 cmd 里面，可以正常使用 conda 命令命令，但在 powershell 中，执行 conda activate 命令时，没有反应。
可以这样解决：
在 powershell 中，以管理员身份运行：
```
Set-ExecutionPolicy RemoteSigned
```
然后运行：
```
conda init powershell
```

这是使用 conda 来自动为 powershell 配置文件。此时启动 powershell，会自动启动 base 环境环境，也能正常激活环境。但速度太慢了。可以输入：
```
conda config --set auto_activate_base false
```
来取消自动启动 base 环境环境。
如果反悔，可输入：
```
conda config --set auto_activate_base true
```