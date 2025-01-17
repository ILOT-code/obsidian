
## 基础编辑
### 基础操作

| 作用  | 按键                    |
| --- | --------------------- |
| 撤销  | Ctrl+Z                |
| 重做  | Ctrl+Y (Ctrl+Shift+Z) |

### 行或块级别操作

| 作用                           | 按键                        |
| ---------------------------- | ------------------------- |
| 剪切当前行                        | Ctrl+X                    |
| 复制当前行                        | Ctrl+C                    |
| 粘贴行                          | Ctrl+V                    |
| 删除当前行                        | Ctrl+Shift+K              |
| 在下方、上方插入行                    | Ctrl+{None,Shift}+Enter   |
| 上移、下移当前行                     | Alt+{Down, Up}            |
| 向下、向上复制当前行                   | Ctrl+Shift+Alt+{Down, Up} |
| 向右、向左缩进当前行或选中行               | Ctrl+{],\[}               |
| 向右、向左缩进当前选中行                 | {None, Shift}+Tab         |
| 给当前行 (或选中行) 添加 (或去除) 行级别注释符号 | Ctrl+/                    |
| 添加 (或去除) 块级注释符号              | Ctrl+Shift+/              |
### 多光标操作

| 作用             | 按键                     |
| -------------- | ---------------------- |
| 选择当前单词，可以连按    | Ctrl+D                 |
| 选择当前行，可以连按     | Ctrl+L                 |
| 选择全部当前单词       | Ctrl+Shift+L           |
| 在当前选择全部行的末尾加光标 | Shift+Alt+I            |
| 在下面、上面插入光标     | Shift+Alt+{Down, Left} |
| 回退上次光标操作       | Ctrl+U                 |

### 折叠操作

| 作用                   | 按键                 |
| -------------------- | ------------------ |
| 折叠、展开当前所在块           | Ctrl+Shift+{\[,]}  |
| 折叠、展开当前块 (以递归方式)(更改) | Ｃtrl+Alt+{\[,]}    |
| 折叠、展开全部块 (更改)        | Ctrl+K Ctrl+{\[,]} |
| 折叠选中部分，展开该创建的选择部分    | Ctrl+K, Ctrl+{,.}  |
| 折叠、展开所选范围外的全部        | Ctrl+K, Ctrl+(-,+) |


### 跳转与选中


| 作用               | 按键                       |
| ---------------- | ------------------------ |
| 跳转到匹配的括号         | Ctrl+Shift+\             |
| 跳转到行首、尾（连按，切换软硬） | {Home, End}              |
| 跳转到文件首、尾         | Ctrl+{Home, End}         |
| 以单词为单位跳转         | Ctrl+{Left, Right}       |
| 删除到当前单词首尾部       | Ctrl+{Backspace, Delete} |
| 滚动屏幕一行           | Ctrl+ {Up, Down}         |
| 滚动屏幕一页 (更改)      | Ctrl+Alt+{Up, Down}      |
| 滚动屏幕一页，光标跟随滚动    | {PageUP, PageDown}       |
| 按跳转方向选中          | Shift+Jump               |
| 设置标记与选择到标记       | Ctrl+K, Ctrl+{B, K}      |
| 智能选择区域，扩展与收缩     | Shift+Alt+{Right, Left}  |

### 文件内部的搜索与替换

| 作用         | 按键          |
| ---------- | ----------- |
| 在当前文件查找    | Ctrl+F      |
| 在当前文件替换    | Ctrl+H      |
| 到下一个查找匹配的项 | Enter       |
| 到上一个查找匹配的项 | Shift+Enter |
| 选择所有匹配的项   | ALT+Enter   |
| 在搜索框中换行    | Ctrl+Enter  |
| 搜索切换大小写敏感  | Alt+C       |
| 搜索切换正则表达式  | Alt+R       |
| 搜索切换整个单词   | Alt+W       |

### 杂项

| 作用            | 按键            |
| ------------- | ------------- |
| 比较活动文件与剪切板    | Ctrl+K, C     |
| 比较活动文件与它的保存版本 | Ctrl+K, D     |
| 按 Tab 来移到焦点   | Ctrl+M        |
| 切换长代码自动换行     | Alt+Z         |
| 删除不必要的空格      | Ctrl+K Ctrl+X |
| 打开设置          | Ctrl+,        |
| 打开快捷键设置       | Ctrl+K Ctrl+S |
| 选择主题          | Ctrl+K Ctrl+T |

## 语法信息

| 作用                   | 按键               |
| -------------------- | ---------------- |
| 触发建议                 | Ctrl+Space       |
| 触发函数提示 (光标在函数括号内)    | Ctrl+Shift+Space |
| 格式化文档                | Ctri+Shift+I     |
| 格式化当前行 (或所选区域)       | Ctrl+K Ctrl+F    |
| 显示其信息 (光标在函数，类，变量上 ) | Ctrl+K Ctrl+I    |
| 转到定义 (更改)            | Ctrl+Alt+D       |
| 转到定义 (在边上打开)(更改)     | Ctrl+Alt+S       |
| 查看定义 (悬浮窗口上)(更改)     | Ctrl+Alt+H       |
| 重构符号                 | Ctrl+Shift+R     |
| 快速修改                 | Ctrl+.           |
| 查看引用 (悬浮窗口)(更改)      | Alt+Shift+D      |
| 查看全部引用 (在左栏显示)(更改)   | Alt+Shift+A      |
| 显示该符号被调用层次结构         | Alt+Shift+H      |
| 重命名符号 (更改)           | Alt+Shift+R      |

## 导航
### 编辑跳转

| 作用              | 按键                    |
| --------------- | --------------------- |
| 跳转到当前工作区的符号     | Ctrl+T                |
| 跳转到行            | Ctrl+G                |
| 跳转到文件           | Ctrl+P                |
| 跳转到当前编辑器的符号     | Ctrl+Shift+O          |
| 切换标签页 (下一个，上一个) | Ctrl+{None,Shift}+Tab |
| 返回，前进           | Ctrl+{Alt, Shift}+-   |
| 回到上一编辑位置        | Ctrl+K, Ctrl+Q        |
| 聚焦到导航路径视图       | Ctrl+Shift+;          |
| 聚焦到导航路径视图并选择    | Ctrl+Shift+.          |
| 跳转到命令输入界面       | Ctrl+Shift+P          |

### 语法信息跳转

| 作用                                | 按键                    |
| --------------------------------- | --------------------- |
| 转到下、上一个问题 (更改)                    | Ctrl+K {Ctrl, None}+E |
| 转到下、上一个引用 (更改)                    | Ctrl+K {Ctrl, None}+N |
| 转到下、上一个搜索结果 (更改)                  | Ctrl+Alt+{N, P}       |
| 转到下 (上) 一个更改或差异, (不在比较器中不可使用)(更改) | Ctrl+Alt+{N, P}       |
| 转到下 (上) 一个突出显示 (更改)               | Ctrl+Alt+{N, P}       |

## 窗口管理

| 作用                        | 按键                     |
| ------------------------- | ---------------------- |
| 新窗口                       | Ctrl+Shift+N           |
| 关闭窗口                      | Ctrl+Shift+W (Ctrl+Q)  |
| 打开最近的文件或文件夹               | Ctrl+R                 |
| 关闭当前编辑器                   | Ctrl+W                 |
| 关闭当前工作目录                  | Ctrl+K F               |
| 向右拆分编辑器                   | Ctrl+\                 |
| 向下拆分编辑器                   | Ctrl+K Ctrl+\          |
| 右侧打开相同的编辑器                | Ctrl+K Ctrl+Shift+\    |
| 聚焦到第 1,2,3 个编辑器组 (不存在就创造) | Ctrl+{1,2,3}           |
| 聚焦左主侧栏                    | Ctrl+0                 |
| 转到左边 (右边) 的编辑器            | Ctrl+{PageUp,PageDown} |
| 将当前编辑器右移 (左移)(更改)         | Ctrl+Shift+{Down,Up}   |
| 将当前编辑器组向方向键移动             | Ctrl+K Arrow           |
| 按方向聚焦到编辑器组（更改）            | Ctrl+K, Ctrl+Arror     |
| 把当前编辑器移到 （上、下一个）编辑器组      | Ctrl+Alt+{left, right} |
| 切换最大化当前编辑器组 (用户)          | Ctrl+K Ctrl+Z          |
## 文件管理
| 按键                                              | 作用                 |
| ----------------------------------------------- | ------------------ |
| 新文件                                             | Ctrl+N             |
| 打开文件                                            | Ctrl+O             |
| 打开文件夹                                           | Ctrl+K Ctrl+O      |
| 在新窗口打开当前编辑器                                     | Ctrl+K O           |
| 保存                                              | Ctrl+S             |
| 另存为                                             | Ctrl+Shift+S       |
| 关闭当前编辑器                                         | Ctrl+W             |
| 关闭当前编辑器组                                        | Ctrl+K W           |
| 关闭当前编辑器组中已保存的文件                                 | Ctrl+K U           |
| 关闭全部编辑器                                         | Ctrl+K Ctrl+W      |
| 重新打开关闭的编辑器 (可连按)(更改)                            | Ctrl+K Ctrl+R      |
| 使当前处于预览的文件保持打开 (预览状态的文件名是斜体的，这是点击其它文件，此文件会被切换掉) | Ctrl+K Enter       |
| 切换编辑器是否固定 (更改)                                  | Ctrl+K, Ctrl+Enter |
| 复制当前文件路径                                        | Ctrl+K P           |
| 在资源管理器中定位到当前文件                                  | Ctrl+K R           |
| 聚焦在文件栏时，复制文件绝对（相对）路径                            | Ctrl+{Shift}+Alt+C |
| 聚焦在文件栏时，在资源管理器中打开                               | Ctrl+Alt+R         |
## 显示
| 作用                  | 按键                   |
| ------------------- | -------------------- |
| 进入 (退出) 全屏          | F11                  |
| 显示菜单栏 (菜单栏不可见时)     | Alt                  |
| 进入 (退出) Zen 模式      | Ctrl+K Z             |
| 放大 (缩小)             | Ctrl+{=,-}           |
| 显示 (折叠) 左主侧栏        | Ctrl+B               |
| 显示 (折叠) 右主侧栏        | Ctrl+Alt+B           |
| 显示资源管理器             | Ctrl+Shift+E         |
| 显示搜索                | Ctrl+Shift+F         |
| 显示替换                | Ctrl+Shift+H         |
| 显示源代码管理             | Ctrl+Shift+G         |
| 显示运行和调试             | Ctrl+Shift+D         |
| 显示扩展                | Ctrl+Shift+X         |
| 显示输出 (更改)           | Ctrl+Shift+T         |
| 显示问题                | Ctrl+Shift+M         |
| 显示调试                | Ctrl+Shift+Y         |
| 显示终端                | Ctrl+`               |
| 新建终端                | Ctrl+Shift+`         |
| 显示 (隐藏) 下栏          | Ctrl+J               |
| 打开系统终端 (到当前目录下)（更改） | Ctrl+Shift+Q         |
| 预览当前 md 文件 (在新编辑器中) | Ctrl+Shift+V         |
| 预览当前 md 文件 (在右边打开)  | Ctrl+K V             |
| 显示通知                | Ctrl+K, Ctrl+Shift+N |
## 搜索

| 作用                                | 按键              |
| --------------------------------- | --------------- |
| 打开搜索                              | Ctrl+Shift+F    |
| 打开替换                              | Ctrl+Shift+H    |
| 切换大小写敏感                           | Alt+C           |
| 切换是否选择整个单词                        | Alt+W           |
| 切换是否使用正则表达式                       | Alt+R           |
| 显示 (隐藏) 搜索的细节                     | Ctrl+Shift+J    |
| 下、上一个搜索结果 (更改)                    | Ctrl+Alt+{N, P} |
| 转到下 (上) 一个更改或差异, (不在比较器中不可使用)(更改) | Ctrl+Alt+{N, P} |
| 转到下 (上) 一个突出显示 (更改)               | Ctrl+Alt+{N, P} |
| 显示下一个搜索项目                         | Down            |
| 显示前一个搜索项目                         | Up              |

## Debug

| 作用         | 按键      |
| ---------- | ------- |
| 设置 (取消) 断点 | F9      |
| 开始调试       | F5      |
| 继续         | F5      |
| 以非调试模式运行   | Ctrl+F5 |
| 暂停         | F6      |
| 单步执行       | F11     |
| 逐过程执行      | F10     |

## Tasks

| 作用     | 按键           |
| ------ | ------------ |
| 运行构建任务 | Ctrl+SHift+B |

## Terminal
聚焦在终端

| 作用            | 按键         |
| ------------- | ---------- |
| 前往最近目录        | Ctrl+G     |
| 运行最近命令（搜索模式）  | Ctrl+R     |
| 运行最近命令 (选择模式) | Ctrl+Alt+R |
## 扩展

| 来源           | 作用                                         | 按键                         |
| ------------ | ------------------------------------------ | -------------------------- |
| copilot      | 对选择内容进行解释 (更改)                             | Ctrl+Alt+E                 |
| copilot      | 把所选内容增加到聊天 (更改)                            | Ctrl+K, Ctrl+Alt+E         |
| copilot      | 在右边栏显示建议 (更改)                              | Alt+Enter                  |
| copilot      | 询问 copilot (悬浮)                            | Ctrl+I                     |
| copilot      | 询问 copilot（左边栏）                            | Ctrl+Alt+I                 |
| Git History  | 显示文件的 git 历史                               | Alt+H                      |
| Git          | 打开更改 (更改)                                  | Alt+M                      |
| Git          | Alt+M 视图中，还原所选范围 (更改)                      | Ctrl+K, Ctrl+Alt+R         |
| Git          | Alt+M 视图中，暂存所选范围                           | Ctrl+K, Ctrl+Alt+S         |
| Git          | Alt+M 视图中，取消暂存所选范围 (更改)                    | Ctrl+K, Ctrl+Alt+N         |
| Metago       | 把当前编辑器移到 （上一个、下一个）编辑器组 (删除)                | Ctrl+K, Ctrl+{left, right} |
| Metago       | 切换到列选择模式 (此时选择文本将按照列的方式进行)                 | Alt+V                      |
| Metago       | 删除到行首，行尾                                   | Shift+{Backspace, Delete}  |
| Metago       | 选中括号中的内容 (是否包括括号本身)。输入起始的 pair (,{来选择 (更改) | Alt+{None,Ctrl}+A          |
| Metago       | 选中 pair 对，不包括其中的内容，修改该括号类型                 | Alt+P                      |
| Metago       | 添加或去除书签（更改）                                | Alt+K                      |
| Metago       | 跳转到下一个，上一个书签位置                             | Alt+{[,]}                  |
| Metago       | 书签菜单                                       | Alt+\                      |
| Metago       | 向前删除，向后删除到任意位置                             | Alt+{Delete, Backspace}    |
| Metago       | 删除到任意位置                                    | Alt+D                      |
| Metago       | 跳转到字符后、前，或智能选择前后                           | Alt+{,./}                  |
| Metago       | 在字符后、前（智能前后）位置插入光标                         | Ctrl+Alt+{.,/}             |
| Metago       | 选择到字符后、前，或智能选择前后                           | Shift+Alt+{.,/}            |
| Metago       | 跳转到下一个或上一个空行                               | Alt+{End, Home}            |
| Metago       | 选择到下一个、上一个空行，可连按，反向按可取消一次选择                | Shift+Alt+{End, Home}      |
| Metago       | 把当前行滚动到底部、中间、顶部 (更改)                       | Alt+{B, N, T}              |
| Metago       | 向下、向上选择行。需要先按 Ctrl+L                       | {Ctrl, Alt}+L              |
| Metago       | 在选中文本时，切换光标在文本的头部还是尾部 (更改)                 | Alt+U                      |
| SSH          | 打开远程菜单                                     | Ctrl+Alt+O                 |
| Clangd       | 切换头文件源文件                                   | Alt+O                      |
| block travel | 块跳转（更改）                                    | Alt+{left, right}          |
| block travel | 选择到上一（下一）块（更改）                             | Ctrl+K, Alt+{left, right}  |
