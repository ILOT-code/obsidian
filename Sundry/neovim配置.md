

[Prerequisites · ayamir/nvimdots Wiki · GitHub](https://github.com/ayamir/nvimdots/wiki/Prerequisites#optional-packages)
安装好前置环境。

需要注意：`sudo apt install yarn` 这个是有问题的，可以使用 npm 来安装 yarn：（来自[Installation | Yarn](https://classic.yarnpkg.com/en/docs/install/#windows-stable)）
```
npm install --global yarn
```

另外，使用 `yarn global add tree-sitter-cli` 安装时，遇到了网络问题，把梯子关了就好了，不知道为什么。
然后上面的命令虽然有 `global` 命令，但还是找不到，需要去 `~/.bashrc` 加上：
```
export PATH="$(yarn global bin):$PATH"
```

详见 [yarn global | Yarn](https://classic.yarnpkg.com/lang/en/docs/cli/global/)


为了 windows 和 neovim 剪切板可以共享，需要安装 win32yank.exe。去它的 github （[GitHub - equalsraf/win32yank: Windows clipboard tool](https://github.com/equalsraf/win32yank)）上下载最新的 release 压缩包。解压后将得到里面的 win32yank. exe 放到 `/usr/local/bin` 目录下。

对于一些 c++项目，可能找不到头文件，因为它们的头文件路径使被配置了的。[Getting started](https://clangd.llvm.org/installation.html) 描述了如何从 cmake 等配置方法来自动生成 clang 配置的方法。就可以找到头文件了。

想要以某个 python 环境进入 nvim，就在命令行先激活它

想要生成 markdown 预览的化，要先安装：
```
npm install -g neovim
```

他会从 wsl2 内部向外打开浏览器来预览当前 markdown，然后不用关闭网页，md 文件做的改动很快会跟新到网页上。
但启动这个网页的速度很慢。



在. ssh 文件夹下，如果存在 config 的话，：
```
Host github.com
    Hostname github.com
    User git
    IdentityFile ~/.ssh/id_rsa.pub

```

git 命令会出现问题，git clone 等命令全会出现权限问题。




### 修改

`D:\src\nvimdots-main\lua\modules\configs\ui\alpha.lua`，将 `dashboard.section.header.val` 更改成了喜欢的开启界面。
`D:\src\nvimdots-main\lua\keymap\ui.lua`，交换了 `<A-j>` 和 `<A-k>` 的功能。
`D:\src\nvimdots-main\lua\core\settings.lua`，增加了：
```
settings["format_disabled_dirs"] = {

    -- Example

    "~/format_disabled_dir",

}
```

`D:\src\nvimdots-main\lua\core\settings.lua`，将字体更改了：
```
settings["gui_config"] = {

    font_name = "FiraCode Nerd Font",

    font_size = 12,

}
```

