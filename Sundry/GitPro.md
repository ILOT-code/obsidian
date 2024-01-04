# 起步
## Git 初识
git 是一种分布式的版本控制工具。
git 以快照的形式存储数据。想象一个桌子摆放了一系列物品，git 并不是记录所有物品的位置和信息，而是给它拍一张照，事后需要信息时就从照片上读出物品信息。
Git 中所有的数据在存储前都计算校验和，然后以校验和来引用。该机制是 SHA-1 散列。
Git 管理着三棵树：工作区、暂存区（索引区）、GIt 仓库目录区
![[Pasted image 20231231125116.png]]
工作区是对项目的某个版本独立提取出来的内容。这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。
暂存区是一个文件，保存了下次将要提交的文件列表信息，一般在 Git 仓库目录中。 按照 Git 的术语叫做“索引”，不过一般说法还是叫“暂存区”。
Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。这是 Git 中最重要的部分，从其它计算机克隆仓库时，复制的就是这里的数据。

## Git 初始配置
Git 自带一个 `git config` 的工具来帮助设置控制 Git 外观和行为的配置变量。这些变量存储在三个不同的位置：

1. `/etc/gitconfig` 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 `git config` 时带上 `--system` 选项，那么它就会读写该文件中的配置变量。 （由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。）
2. `~/.gitconfig` 或 `~/.config/git/config` 文件：只针对当前用户。 你可以传递 `--global` 选项让 Git 读写此文件，这会对你系统上 **所有** 的仓库生效。
3. 当前使用仓库的 Git 目录中的 `config` 文件（即 `.git/config`）：针对该仓库。 你可以传递 `--local` 选项让 Git 强制读写此文件，虽然默认情况下用的就是它。 （当然，你需要进入某个 Git 仓库中才能让该选项生效。）

每一个级别会覆盖上一级别的配置，所以 `.git/config` 的配置变量会覆盖 `/etc/gitconfig` 中的配置变量。

你可以通过以下命令查看所有的配置以及它们所在的文件：

```console
$ git config --list --show-origin
```

Git 需要配置用户信息：
```console
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

这是给全局进行配置的。如果有某个项目，你想要更改此项目的用户信息，那就在该目录下运行没有 `--global` 命令的配置信息。

Git 配置文本编辑器：

```console
$ git config --global core.editor nvim
```

检查配置信息：
```console
git config --list
```

这会列出 Git 能找到的配置信息。你可能会看到重复的变量名，因为 Git 会从不同的文件中读取同一个配置（例如：`/etc/gitconfig` 与 `~/.gitconfig`）。这种情况下，Git 会使用它找到的每一个变量的最后一个配置。

你可以通过输入
```
git config <key>
eg. git config core.editor
```

来检查 Git 的某一项配置。

##  获取帮助

若你使用 Git 时需要获取帮助，有三种等价的方法可以找到 Git 命令的综合手册（manpage）：

```console
$ git help <verb>
$ git <verb> --help
$ man git-<verb>
```

例如，要想获得 `git config` 命令的手册，执行

```console
$ git help config
```

另外，将 `--help` 改成 `-h` 可以获得精简的帮助手册：
```
git add -h
```


# Git 基础

## 获取 Git 仓库

```console
git init
```

这会在当前文件夹初始化一个 Git 仓库。
```
git clone <url> [dirpath]
```

克隆一个仓库当前目录下。如果指定了 `[dirpath]` 则会克隆到那个目录，比如 `../exp`
## log, add ,commit

```
git status        检查当前状态
git add filename  跟踪未跟踪的文件或是将修改的文件加入到暂存区
git add dirname   对文件夹下的文件递归执行git add
```

精简状态输出：
```console
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

新添加的未跟踪文件前面有 `??` 标记，新添加到暂存区中的文件前面有 `A` 标记，修改过的文件前面有 `M` 标记。输出中有两栏，左栏指明了暂存区的状态，右栏指明了工作区的状态。例如，上面的状态报告显示： `README` 文件在工作区已修改但尚未暂存，而 `lib/simplegit.rb` 文件已修改且已暂存。 `Rakefile` 文件已修改，暂存后又作了修改，因此该文件的修改中既有已暂存的部分，又有未暂存的部分。其实左栏是比较暂存区和 Git 仓库，而右栏是比较工作区和暂存区。

创建 `.gitignore` 文件来忽略一些文件。在最简单的情况下，一个仓库可能只根目录下有一个 `.gitignore` 文件，它递归地应用到整个仓库中。 然而，子目录下也可以有额外的 `.gitignore` 文件。子目录中的 `.gitignore` 文件中的规则只作用于它所在的目录中。


```
git diff                 比较工作目录中文件和暂存区域快照之间的差异
git diff --staged        比对已暂存文件与最后一次提交的文件差异
git diff --cached        同上
git commit               将暂存区提交，会打开编辑器来输入mesage
git commit -m "meg"      提交，并同时附加信息
git commit -a            提交，自动把所有已经跟踪过的文件暂存起来并提交，从而跳过 `git add` 步骤
git rm file              删除文件并暂存此次删除。相当于rm file加上git add file
git mv filefrom fileto   重命名文件，相当于mv filefrom fileto,git rm filefrom,git add fileto
git log                  查看提交历史
git log --pretty=oneline

```

```
git commit --amend [-m "msg"]
```

这个命令会将暂存区中的文件提交。如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令），那么快照会保持不变，而你所修改的只是提交信息。最终你只会有一个提交——第二次提交将代替第一次提交的结果。

## 重置操作

|  | HEAD | Index | Workdir | WD Safe |  |
| ---- | ---- | ---- | ---- | ---- | ---- |
| **Commit Level** |  |  |  |  |  |
| `reset --soft [commit]` | REF | NO | NO | YES |  |
| `reset [commit]` | REF | YES | NO | YES |  |
| `reset --hard [commit` | REF | YES | YES | NO |  |
| `checkout [commit]` | HEAD | YES | YES | YES |  |
| **File Level** |  |  |  |  |  |
| `reset [commit] <paths>` | NO | YES | NO | YES |  |
| `checkout [commit] <paths>` | NO | YES | YES | NO |  |
不指定· `[commit]` 的话默认就是当前的 HEAD。HEAD” 一列中的 “REF” 表示该命令移动了 HEAD 指向的分支引用，而 “HEAD” 则表示只移动了 HEAD 自身。特别注意 'WD Safe?' 一列——如果它标记为 **NO**，那么运行该命令之前请考虑一下。
在执行 `git checkout [commit]` 的时候，是安全的，Git 会自动进行检查，并禁止会造成工作区内容丢弃的操作。另外，执行 `checkout` 命令时，不指定 `commit` 的话，由于默认是 HEAD，Git为了安全起见，这时 Index 和 Workdir 实际都不会改变。需要额外指定参数：
```
git checkout .              放弃工作区全部更改（变得和暂存区一样）
git checkout -- filename    放弃该文件工作区的更改（变得和暂存区一样）
git checkout -f             放弃工作区和暂存区的更改（变得和HEAD一样）
```


## 远程仓库
```
git remote                          列出所有远程服务器的简写
git remote -v                       显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL
git remote add <shortname> <url>    添加远程仓库，并指定简写
git fetch <remote>                  拉取全部该远程仓库中有而本地没有的数据,执行完成后，你将会拥有那
                                    个远程仓库中所有分支的引用，可以随时合并或查看
git fetch --all                     拉取所有远程仓库
git fetch <remote> <branch>         只拉取某个分支
git push <remote> <branch>          将本地分支推送给远程仓库
git remote show <remote>            列出该远程仓库的详细信息
git remote rename namef nameto      重命名远程仓库的简写名字
git remote rm name                  在本地删除该远程仓库
```

## 标签
Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）。
轻量标签很像一个不会改变的分支——它只是某个特定提交的引用。
而附注标签是存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间， 此外还有一个标签信息，并且可以使用 GNU Privacy Guard （GPG）签名并验证。 通常会建议创建附注标签，这样你可以拥有以上所有信息。但是如果你只是想用一个临时的标签， 或者因为某些原因不想要保存这些信息，那么也可以用轻量标签

```
git tag                         列出标签
git tag -l "2.*"                列出匹配的标签
git tag -a v1.0 -m "msg"        为当前HEAD打上附注标签(-a选项的意义)，并附上标签信息
git tag v1.0                    打上轻量标签，不需要附注信息
git show v1.0                   显示标签信息
git tag -a v1.2 9fceb02         为某个特定提交打标签

```

默认情况下，`git push` 命令并不会传送标签到远程仓库服务器上。在创建完标签后你必须显式地推送标签到共享服务器上。

```
git push origin v1.0                    推送一个标签
git push origin --tags                  把所有不在远程仓库服务器上的标签全部传送到那里
git tag -d v1.0                         在本地删除标签，此时远程仓库的标签不会被删除，需要更新：
git push <remote> :refs/tags/<tagname>  本地删除标签后，将删除标签推送给远程仓库
git push origin --delete <tagname>      删除远程仓库标签
```

检出标签
```
git checkout v1.0         检出到v1.0标签，此时会处于“分离头指针”状态
```

在“分离头指针”状态下，如果你做了某些更改然后提交它们，标签不会发生变化，但你的新提交将不属于任何分支，并且将无法访问，除非通过确切的提交哈希才能访问。因此，如果你需要进行更改，比如你要修复旧版本中的错误，那么通常需要创建一个新分支：
```console
git checkout -b version2 v1.0   移动到标签v1.0并在这里创建新分支，这就避免了“头指针分离状态”
```


## 别名
```console
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

```console
$ git config --global alias.unstage 'reset HEAD --'
```

这会使下面的两个命令等价：

```console
$ git unstage fileA
$ git reset HEAD -- fileA
```

# 分支 ：
## 本地分支
```
git branch branchname            创建分支
git checkout branchname          切换到分支
git checkout -b branchname       创建分支并切换过去
git branch -d name               删除分支
git merge branchname             合并该分支到当前分支
git branch                       列出分支表
git branch -v                    查看每一个分支的最后一次提交
git branch --merged              查看已合并到当前分支的分支，这些分支通常可以删除
git branch --no-merged           查看未合并到当前分支的分支
git branch --merged [branchname] 查看已合并到branchname的分支
```

## 远程分支
克隆一个仓库后，只会在本地创建一个 master 分支并关联远程的 master 分支
```
git branch -r                             列出远程仓库分支表
git branch -a                             显示本地和远程所有分支
git checkout -b name1 origin/name2        创建分支name1，起始点和远程分支name2一样，并自动关联
git checkout --track origin/name          上面命令的简写(并且name1=name2)
git checkout name                         如果该分支本地不存在，刚好只有一个名字与之匹配的远程分
                                          支，那么Git 就会为你创建一个跟踪分支
git push <remote> <branch>                将本地的branch推送(远程没有的话就创建)
git push origin localname:remomename      推送本地分支到远程(远程没有的话就创建)
git merge origin/name                     将远程分支(注意先fetch到最新)与当前分支合并
git branch -u origin/name                 将当前分支与该远程分支关联
git branch --set-upstream-to=origin/name  同上
git push <remote> --delete <branch>         删除本地分支后，删除对应的远程分支
git push <remote> :<branch>                 同上
```

```
git branch -vv                            显示本地分支跟踪的是什么远程分支，以及是否领先或落后
```
需要重点注意的一点是这些数字的值来自于你从每个服务器上最后一次抓取的数据。 这个命令并没有连接服务器，它只会告诉你关于本地缓存的服务器数据。 如果想要统计最新的领先与落后数字，需要在运行此命令前抓取所有的远程仓库。 可以像这样做：
```console
git fetch --all
git branch -vv
```

## 变基

```
git rebase <branch>                     将当前分支内容，以<branch>为基础，进行变基
git rebase <basebranch> <topicbranch>   将topicbranch内容，以basebranch为基础，变基
```

```console
$ git rebase --onto master server client
```

以上命令的意思是：“取出 `client` 分支，找出它从 `server` 分支分歧之后的补丁，然后把这些补丁在 `master` 分支上重放一遍，让 `client` 看起来像直接基于 `master` 修改一样”。



