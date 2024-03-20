********
# Ubuntu
[每位 Ubuntu 18.04 用户都应该知道的快捷键 - 知乎](https://zhuanlan.zhihu.com/p/45535756)
## 启动

`Ctrl-Alt-T`：启动终端
`Super-F1`：启动帮助浏览器
## 导航

`Super-Home`：切换到第一个工作区
`Super-End`：切换到最后一个工作区

`Alt-Tab`：切换窗口
`Super-Tab`：切换应用程序

"Alt- \`" 和"Super-\`"：在应用程序的窗口之间切换窗口

`Alt-Esc`：直接切换窗口
`Super-d`：显示桌面

`Ctrl-Alt-左(右)`：切换工作区
## 截屏

`Print`：交互式截屏
`Alt-Print`：对窗口截屏
`Shift-Print`：截屏
`Shift-Ctrl-Alt-R`：交互式录屏

## 窗口

`Alt-F4`: 关闭窗口
`Alt-F10`: 切换到最大状态
`Super-左(右)`：将窗口推往左 (右)
`Super-上(下)`：最大化窗口 (恢复窗口一般大小)
`Super-h`：最小化窗口
`Alt-Space`：激活窗口菜单

### 系统

`Super-a`：显示全部应用程序
`Super`：显示当前工作区全部窗口
`Super-v`：显示通知
`Super-l`：锁屏

# Vscode

[Visual Studio Code Key Bindings](https://code.visualstudio.com/docs/getstarted/keybindings)

在 `Ctrl+P` 下输入 `>` 又可以回到主命令框 `Ctrl+Shift+P` 模式。

在`Ctrl+P`窗口下还可以

- 直接输入文件名，快速打开文件
    
- `?` 列出当前可执行的动作
    
- `!` 显示Errors或Warnings，也可以`Ctrl+Shift+M`
    
- `:` 跳转到行数，也可以`Ctrl+G`直接进入
    
- **@ 跳转到symbol（搜索变量或者函数），也可以Ctrl+Shift+O直接进入**
    
- `@:`根据分类跳转symbol，查找属性或函数，也可以`Ctrl+Shift+O`后输入`:`进入
    
- `#` 根据名字查找symbol，也可以`Ctrl+T`
    

## Basic Editing

| 作用                       | 按键                  |
| ------------------------ | ------------------- |
| 剪切当前行                    | Ctrl+X              |
| 复制当前行                    | Ctrl+C              |
| 粘贴                       | Ctrl+V              |
| 删除当前行                    | Ctrl+Shift+K        |
| 在下方插入行                   | Ctrl+Enter          |
| 在上方插入行                   | Ctrl+Shift+Enter    |
| 上移当前行                    | Alt+Down            |
| 下移当前行                    | Alt+Up              |
| 向下复制当前行(失败)              | Ctrl+Shift+Alt+Down |
| 向上复制当前行(失败)              | Ctrl+Shift+Alt+Up   |
| 撤销                       | Ctrl+Z              |
| 重做                       | Ctrl+Y              |
| 回到上次光标位置                 | Ctrl+U              |
| 选择当前单词，可以连按              | Ctrl+D              |
| 选择当前行，可以连按               | Ctrl+L              |
| 选择全部当前单词                 | Ctrl+Shift+L        |
| 在当前选择全部行的末尾加光标           | Shift+Alt+I         |
| 在下面插入光标                  | Shift+Alt+Down      |
| 在上面插入光标                  | Shift+Alt+Up        |
| 缩进当前行或选中行 (向右)           | Ctrl+]              |
| 缩进当前行或选中行 (向左)           | Ctrl+[              |
| 缩进选中行 (向右)               | Tab                 |
| 缩进当前行选中行 (向左)            | Shift+Tab           |
| 跳转到匹配的括号                 | Ctrl+Shift+\        |
| 跳转到行首                    | Home                |
| 跳转到行尾                    | End                 |
| 跳转到文件首                   | Ctrl+Home           |
| 跳转到文件首                   | Ctrl+End            |
| 以单词为单位跳转                 | Ctrl+Left(Right)    |
| 滚动屏幕一行                   | Ctrl+Up(Down)       |
| 滚动屏幕一页(更改)               | Ctrl+Alt+Up(Down)   |
| 按方向选中                    | Shift+Arrow         |
| 折叠当前所在块                  | Ctrl+Shift+[        |
| 展开当前所在块                  | Ctrl+Shift+]        |
| 折叠当前块(以递归方式)(更改)         | Ｃtrl+Alt+[          |
| 展开当前块(以递归方式)(更改)         | Ctrl+Alt+]          |
| 折叠全部块(更改)                | Ctrl+K Ctrl+[       |
| 展开全部块(更改)                | Ctrl+K Ctrl+]       |
| 给当前行(或选中行)添加(或去除)行级别注释符号 | Ctrl+/              |
| 添加(或去除)块级注释符号            | Ctrl+Shift+/        |
| 在当前文件查找                  | Ctrl+F              |
| 在当前文件替换                  | Ctrl+H              |
| 到下一个查找匹配的项               | Enter               |
| 到下一个查找匹配的项               | Shift+Enter         |
| 选择所有匹配的项                 | ALT+Enter           |
| 在搜索框中换行                  | Ctrl+Enter          |
| 搜索切换大小写敏感                | Alt+C               |
| 搜索切换正则表达式                | Alt+R               |
| 搜索切换整个单词                 | Alt+W               |
| 按 Tab 来移到焦点              | Ctrl+M              |
| 切换长代码自动换行                | Alt+Z               |

## Rich Languages Editing

| 作用                   | 按键               |
| -------------------- | ---------------- |
| 触发建议                 | Ctrl+Space       |
| 触发函数提示(光标在函数括号内)     | Ctrl+Shift+Space |
| 格式化文档                | Ctri+Shift+I     |
| 格式化当前行(或所选区域)        | Ctrl+K Ctrl+F    |
| 显示其信息 (光标在函数，类，变量上 ) | Ctrl+K Ctrl+I    |
| 转到定义(更改)             | Ctrl+K Ctrl+D    |
| 转到定义(在边上打开)(更改)      | Ctrl+K Ctrl+L    |
| 查看定义(悬浮窗口上)(更改)      | Ctrl+K Ctrl+H    |
| 快速修改                 | Ctrl+.           |
| 查看引用 (悬浮窗口)(更改)      | Alt+Shift+D      |
| 查看全部引用 (在左栏显示)       | Alt+Shift+F12    |
| 显示该符号被调用层次结构         | Alt+Shift+H      |
| 重命名符号 (更改)           | Alt+Shift+R      |
| 智能选择区域 (扩展)          | Shift+Alt+Right  |
| 智能选择区域 (收缩)          | Shift+Alt+Left   |
| 删除不必要的空格             | Ctrl+K Ctrl+X    |

## Navigation

|作用|按键|
|---|---|
|在当前工作区中搜索符号|Ctrl+T|
|转移到行|Ctrl+G|
|快速打开文件|Ctrl+P|
|转移到符号|Ctrl+Shift+O|
|显示问题栏|Ctrl+Shift+M|
|转到下一个问题(更改)|Ctrl+K Ctrl+E|
|转到上一个问题(更改)|Ctrl+K E|
|输入命令|Ctrl+Shift+P|
|切换标签页|Ctrl+Tab|
|返回|Ctrl+Alt+-|
|前进|Ctrl+Shift+-|
|聚焦到导航路径视图|Ctrl+Shift+;|
|聚焦到导航路径视图并选择|Ctrl+Shift+.|

## Edit/Window Management

| 作用               | 按键                    |
| ---------------- | --------------------- |
| 新窗口              | Ctrl+Shift+N          |
| 关闭窗口             | Alt+F4                |
| 关闭当前编辑器          | Ctrl+W                |
| 关闭当前工作目录         | Ctrl+K F              |
| 向右拆分编辑器          | Ctrl+\                |
| 向下拆分编辑器          | Ctrl+K Ctrl+\         |
| 聚焦到第1,2,3个编辑器组   | Ctrl+(1,2,3)          |
| 聚焦左主侧栏           | Ctrl+0                |
| 转到左边(右边)的编辑器     | Ctrl+PageUp(PageDown) |
| 将当前编辑器右移(左移)(更改) | Ctrl+Shift+Down(Up)   |
| 将当前编辑器组向方向键移动    | Ctrl+K Arrow          |

## File Management

| 按键                                             | 作用            |
| ---------------------------------------------- | ------------- |
| 新文件                                            | Ctrl+N        |
| 打开文件                                           | Ctrl+O        |
| 打开文件夹                                          | Ctrl+K Ctrl+O |
| 在新窗口打开当前编辑器                                    | Ctrl+K O      |
| 保存                                             | Ctrl+S        |
| 另存为                                            | Ctrl+Shift+S  |
| 关闭当前编辑器                                        | Ctrl+W        |
| 关闭当前编辑器组                                       | Ctrl+K W      |
| 关闭当前编辑器组中已保存的文件                                | Ctrl+K U      |
| 关闭全部编辑器                                        | Ctrl+K Ctrl+W |
| 重新打开关闭的编辑器(可连按)(更改)                            | Ctrl+K Ctrl+R |
| 使当前处于预览的文件保持打开(预览状态的文件名是斜体的，这是点击其它文件，此文件会被切换掉) | Ctrl+K Enter  |
| 复制当前文件路径                                       | Ctrl+K P      |
| 在资源管理器中定位到当前文件                                 | Ctrl+K R      |

## Display

| 作用               | 按键           |
| ---------------- | ------------ |
| 进入(退出)全屏         | F11          |
| 显示菜单栏 (菜单栏不可见时)  | Alt          |
| 进入(退出)Zen模式      | Ctrl+K Z     |
| 放大(缩小)           | Ctrl+=(-)    |
| 显示(折叠)左主侧栏       | Ctrl+B       |
| 显示(折叠)右主侧栏       | Ctrl+Alt+B   |
| 显示资源管理器          | Ctrl+Shift+E |
| 显示搜索             | Ctrl+Shift+F |
| 显示替换             | Ctrl+Shift+H |
| 显示源代码管理          | Ctrl+Shift+G |
| 显示运行和调试          | Ctrl+Shift+D |
| 显示扩展             | Ctrl+Shift+X |
| 显示输出 (更改)        | Ctrl+Shift+T |
| 显示问题             | Ctrl+Shift+M |
| 显示调试             | Ctrl+Shift+Y |
| 显示终端             | Ctrl+`       |
| 新建终端             | Ctrl+Shift+` |
| 显示 (隐藏) 下栏       | Ctrl+J       |
| 打开系统终端(到当前目录下)   | Ctrl+Shift+C |
| 预览当前md文件(在新编辑器中) | Ctrl+Shift+V |
| 预览当前md文件(在右边打开)  | Ctrl+K V     |

## Search

| 作用           | 按键           |
| ------------ | ------------ |
| 打开搜索         | Ctrl+Shift+F |
| 打开替换         | Ctrl+Shift+H |
| 切换大小写敏感      | Alt+C        |
| 切换是否选择整个单词   | Alt+W        |
| 切换是否使用正则表达式  | Alt+R        |
| 显示(隐藏)搜索的细节  | Ctrl+Shift+J |
| 下一个搜索结果 (更改) | Ctrl+Alt+N   |
| 上一个搜索结果 (更改) | Ctrl+Alt+P   |
| 显示下一个搜索项目    | Down         |
| 显示前一个搜索项目    | Up           |

## Preferences

|作用|按键|
|---|---|
|打开设置|Ctrl+,|
|打开快捷键设置|Ctrl+K Ctrl+S|
|选择主题|Ctrl+K Ctrl+T|

## Debug

|作用|按键|
|---|---|
|设置(取消)断点|F9|
|开始调试|F5|
|继续|F5|
|以非调试模式运行|Ctrl+F5|
|暂停|F6|
|单步执行|F11|
|逐过程执行|F10|

## Tasks

|作用|按键|
|---|---|
|运行构建任务|Ctrl+SHift+B|

## Extensions


| 来源             | 作用             | 按键         |
| -------------- | -------------- | ---------- |
| copilot        | 对选择内容进行解释      | Ctrl+Alt+E |
| copilot        | 在右边栏显示建议       | Alt+Enter  |
| Bracket Select | 选择括号内内容，可连按    | Alt+A      |
| Bracket Select | 选中括号内内容，（包括括号） | Ctrl+Alt+A |
| Git History    | 显示文件的 git 历史   | Alt+H      |
| Git            | 打开更改           | Alt+M      |








