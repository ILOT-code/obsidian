一个文件是就是缓冲区，一个窗口可显示多个文件。而一个标签则对应了其窗口，窗口去显示打开的文件（缓冲区）。在文件树中，"标签"其实是 buffer。buffer 是全部标签共享的
大部分命令能能和数字连用
## 文件读取保存

- 打开文件
	- 终端下: `vim filename` 打开文件
	- 终端下: `vim +n filename` 打开文件后，定位到第 n 行
	- 终端下：`vim file1 file2 file3 ` 打开 3 个文件
	- 终端下： `vim -o filea fileb filec` 垂直分割打开 3 个文件
	- 终端下： `vim -O filea fileb filec` 水平分割打开 3 个文件
	- `:e filename`: 打开文件 filename, 可以是不存在的文件
	- `:open filename`: 同上
- 多文件的切换
	- `:next` 和 `:n`：将当前窗口或缓冲区替换为下一个文件
	- `:previous` 和 `:Next` 和 `:N`：将当前窗口或缓冲区替换为上一个文件
	- `kn` 和 `kN`: 将当前窗口或缓冲区替换为下 (上) k 个文件
	- `:first`: 将当前窗口或缓冲区替换为第一个文件
	- `:last`: 将当前窗口或缓冲区替换为最后一个文件
	- `:args`: 显示当前文件名
	- `:bn`: 将当前窗口或缓冲区替换为下一个文件
	- `:bp`: 将当前窗口或缓冲区替换为前一个文件
	- `:e#` 和 "ctrl ^": 将当前窗口或缓冲区替换为上一次编辑的文档
	- `:ls` 和 `:files` 和 `:buffers`：可以列出目前缓冲区中的所有文档。加号 + 表示缓冲区已经被修改过了。＃代表上一次编辑的文档，%是目前正在编辑中的文档
	- `:bn`：n 是数字，切换到第 n 个文件
	- `:f` 和 "ctrl g": 显示当前文档信息
- 多文件的关闭
	- `:q` 和 `:close` 和 `<ctrl w>c` （wq）关闭此窗口（buffer 不关闭）
	- `:only` 和 `<ctrl w>o`: 关闭除当前窗口外的其它窗口
	- `:qa`: 关闭所有窗口，`a` 作为一个参数表示全部文件。
- 窗口切换与分割
	- “ctrl ww ”: 窗口切换
	- "ctrl w (hjkl)": 按照选定的方向进行切换
	- `:snext :sprevious :sNext :sfirst :slast`: 垂直分割窗口并切换到那个文件
	- `:sall`：以垂直窗口显示所有文件
	- `:vs(p) filename`：(p 可选可不选)将当前窗口水平分割并切换到 filename，filename 可以不存在。
	- `:sp filename`: 将当前窗口垂直分割并切换到 filename，filename 可以不存在。
	- `vertical resize -2`：水平方向减小窗口
	- `vertical resize +2`：水平方向增大窗口
	- `:resize +10`：垂直方向增大窗口
	- `<ctrl w>=`: 窗口比例相等
- 标签
	- `:tabnew`：新建标签
	- `:tabfind file`：在新标签页打开文件
	- `:tabs`：显示全部标签和信息
	- `:tabn`：切到下一标签页
	- `:tabp` 和 `tabN`：前标签页
	- `:tabc`：关闭当前标签页
	- `:tabo`：关闭其它标签页
	- `:tabfirst`：切到第一个标签
	- `:tablast`：切到最后一个标签
- 标记
	- `m(a-zA-Z)`: 给当前位置设立标记，一共有 52 个标记可以设立
	- `'a`: 移动到标记 a 的那一行软行首
	- `` a `： 移动到标记 a 的那个位置 
	- `:marks`：列出所有标记、
	- `:delmarks a b c`：删除 abc 标记
	- `:delamrks!`：删除全部标记
	- 除了自定义的标记，还有一些默认的标记
		- `.`：当前 buffer 最近一次改动的位置
		- `^`：insert 模式下，光标最后的位置
		- `'`：当前 buffer，光标跳转之前的位置
		- `"`：上次退出该文件时，光标位置
		- `[`：上次修改开始处
		- `]`: 上次修改结束处
		- `0-9`：最近的文件
- 文件保存与离开
	- `:w`: 将内容写入文件
	- `:w!`：强制写入文件
	- `:q`: 离开文件
	- `:q!`: 放弃已做的更改，强行离开
	- `:wq`: 保存并离开
	- `wqa`: a 表示全部已打开的文件
	- `:ZZ`: 保存并离开
	- `:ZQ`: 放弃已做的更改，强行离开
	- `:w filename`: 将文件内容存储为 filename，相当于另存为
	- `:w! filename`: 如果 filename 已存在，上面的命令会报错。这回强行将文件内容存储为 filename
	- `:w >> filename`: 向 filename 后面追加内容
	- `:w! >> filename`: 如果 filename 不存在，上面会报错。没有的话就会创建向 filename 后面追加内容
	- `:100,$w >> filename`：当前文件的第 100 行到最后一行追加到 filename
	- `:.,100w filename`: 当前行到第 100 行的内容写入 filename
	- `:'<,'> w filename`:  visual 模式下将选中内容写入 filename。选择后按 `:` 会自动生成前面的范围描述
	- `:r filename`: 将 filename 这个档案内容加到游标所在行后面
	- `:f filename`: 将当前内容写入 filename，但不保存。


## 命令
- `:! command`: 暂时离开 vim ，去终端执行 command
- `:set nu`: 显示行号
- `:set nonu`: 取消行号
- `:sp | terminal` 垂直窗口打开终端
- `:vsp | terminal` 水平窗口打开终端
## 模式切换命令
- 在 normal 模式下，按下面的键进入 insert 模式：
	- “i”：从光标当前位置开始输入文件。  
	- “I”：在光标所在行的行首插入。  
	- “a”：从目前光标所在位置的下一个位置开始输入文字。  
	- “A”：在光标所在行的行末插入。  
	- “o”：在下面插入一行  
	- “O”：在上面插入一行。  
	- “s”：删除光标后的一个字符，然后进入插入模式。  s 一般不用
	- “S”：删除光标所在的行，然后进入插入模式。S 一般不用
- 在 normal 模式下，按下面的键进入 visual 模式：
	- v: 光标选取
	- V：按行选取
- 在 normal 模式下，按下面的键进入 replace 模式
	- r: 输入一个字符取代当前字符，只执行一次，执行完自动返回 normal 模式。
	- R: 输入一个字符取代当前字符，光标移动到下一个位置继续执行，按 esc 返回 normal 模式。
## 光标移动
- 小范围移动：h j k l 和方向键，可以和数字组合，5 h, 等。
- 按单词移动
	- w：光标跳到下个词的开头。  
	- e：光标跳到当前或下个字的字尾。  
	- b：光标回到当前字或上个字的开头
- 屏幕内跨行移动
	- H: 移动到当前屏的首行。
	- L: 移动到当前屏的尾行。
	- M: 移动到当前屏的中间行。
	- nG: 移动到第 n 行行首
	- n\<Enter\>: 向下移动 n 行
	- +：移动到下一行的软行首
	- -：移动到上一行的软行首
	- `:n`: 到第 n 行
- 翻页
	- “ctrl+b”：屏幕往下移动一页。  
	- “ctrl+f”：屏幕往上移动一页。  
	- “ctrl+u”：屏幕往下移动半页。  
	- “ctrl+d”：屏幕往上移动半页
	- “ctrl+e": 屏幕往上移动一行
	- "ctrl+y": 屏幕往下移动一行
	- zt : 光标所在字符不动，将当前行移动到屏幕顶部.
	- zz: 光标所在字符不动，将当前行移到屏幕中间。
	- zb: 光标所在字符不动，将当前行移到屏幕底部。
- 行内移动
	- 0：移到行首（硬）
	- |: 行首（硬）
	- \$: 移到行尾（硬）
	- \_: 行首（软）
	- ^: 行首（软）
	- g\_：行尾（软）
	- n\<space\>: 向右移动 n 个字符
- 段落移动
	- {: 移动到此段段首或上一段段首
	- }: 移动到此段段尾或下一段段尾
	- (: 和{类似
	- )：和}类似
- 文本首尾：
	- gg: 文本首
	- G：文本尾



## 剪切删除复制粘贴重做
x, y, d, p 分别对应剪切复制删除粘贴，vim 中有很多寄存器，可以通过 `:reg` 查看寄存器内容。
- 无名寄存器 ("")：进行任何含有复制的操作时（像 x, y, d），无名寄存器都会保存其内容，同该操作的特定寄存器内容一样。粘贴时，若无指定，则从此粘贴。
- 复制专用寄存器 ("0)：通过 y 命令复制的内容，会保存到寄存器"0中。
- 删除专用寄存器 ("1-"9)：通过 d 或 c 命令，删掉的内容，会保存打"1-"9 这 9 个寄存器中。最新删除的内容，会在"1 中，其他顺延。
- 命名寄存器 ("a-"z)：可以将重要内容放到命名寄存器"a-"z 中，一共 26 个。
- 黑洞寄存器 ("\_d)：放到这个寄存器的内容，将不会放到任何其他寄存器中，相当于彻底删除内容。
- 系统剪贴板 ("+)：通过"+寄存器可以把内容复制到系统剪贴板，也可以从系统剪贴板粘贴内容但 Vim 中。
使用规则就像：`"ap`，名字加操作，名字包括了前面的双引号。
- 剪切
	- x: 剪切光标位置字符，可与数字连用 3 x（往后删）
	- X: 剪切光标前面的字符，可与数字连用 3 X（往前删）
- 删除：删除的内容同样会保存
	- dw: 删除当前字符到，按下 w 之后到达的那个字符（不包括）之间的全部。可以和数字连用
	- de: 删除当前字符到，按下 e 之后到达的那个字符（包括）之间的全部。
	- db: 删除当前字符前一个字符到。按下按下 b 之后到达的那个字符（包括）之间的全部
	- d0：删除当前字符前一个字符到行首的所有数据 
	- d$：删除当前字符到行尾的所有数据
	- dd: 删除当前行
	- dj: 删除当前行和下一行
	- dk：删除当前行和前一行
	- dgg: 删除当前行到第一行
	- d1G: 删除当前行到第一行
	- dG: 删除当前行到最后一行
	- d 后面可以和其它光标移动连用，和行的连用就是删除当前行和目标行之间的全部行，和行内操作的连用：向后的话就是删除当前字符到目标字符间内容，向前的话就不包括当前字符。
- 复制
	- yw: 复制当前字符到，按下 w 之后到达的那个字符（不包括）之间的全部。
	- ye: 复制当前字符到，按下 e 之后到达的那个字符（包括）之间的全部
	- yb: 复制当前字符前一个字符到。按下按下 b 之后到达的那个字符（包括）之间的全部
	- y0：复制当前字符前一个字符到行首的所有数据  
	- y$：复制当前字符到行尾的所有数据
	- yy: 复制当前行
	- yj: 复制当前行和下一行
	- yk: 复制当前行和前一行
	- ygg: 复制当前行到第一行的全部数据
	- y1G: 复制当前行到第一行的全部数据
	- yG: 复制当前行到最后一行的全部数据
	- y 后面可以和其它光标移动连用，和行的连用就是复制当前行和目标行之间的全部行，和行内操作的连用：向后的话就是复制当前字符到目标字符间内容，向前的话就不包括当前字符。
- 重做命令 c，删除的数据会放入寄存器，执行完后进入 insert 模式
	- cw: 删除当前字符到该单词末尾的全部数据，并放入寄存器
	- ce: 和 cw 一样
	- cb: 删除当前字符前一个字符到单词首的全部数据，放入寄存器
	- c 后面可以和其它光标移动连用，和行的连用就是重做当前行和目标行之间的全部行，和行内操作的连用：向后的话就是重做当前字符到目标字符间内容，向前的话就不包括当前字符。
- 粘贴
	- p: 若复制了行或段，粘贴到当前行下一行，光标也移动到下一行；复制单词，则粘贴到当前字符下一位置
	- P: 若复制了行或段，粘贴到当前行前一行，光标也移到到前一行；复制单词，则粘贴到当前字符前一位置

## 编辑键
- u: 撤销上次操作
- "ctrl r": 重做撤销的操作
- .: （小数点）重做前一个命令
- J: 将当前行和下一行合并

## 命令总览
| 命令 | 示例 | 用途 |
| ---- | ---- | ---- |
| :\[range\]delete \[x] | :.,+2d a | 删除指定范围内的行到寄存器 x |
| :\[range]yank \[x] | :.,+2y a | 复制指定范围的行到寄存器 x |
| :\[line]put \[x] | :.put a | 在指定行后粘贴寄存器 x 中的内容 |
| :\[range]copy \[line] | :-2,.copy -5 | 把指定范围内的行拷贝到指定的行下面 |
| :\[range]move \[line] | :.,+2m -5 | 把指定范围内的行移动到指定行下面 |
| :\[range]join | :.,+2j | 连接指定范围内的行 |
| :\[range]normal \[commands] | :.,+2normal dd | 对指定范围内的每一行执行普通模式命令\[commands] |
| :\[range]substitute/{patern}/{string}/{flags} | :%s/for/to/g | 对指定范围替换 |
| :\[range]global/{pattern}/\[cmd] | :g/^$/d | 对指定范围内匹配了{pattern}的所有行执行Ex命令\[cmd] |
## 查找和替换
### 查找
- 快捷搜索
	- \*: 向下搜索匹配的单词
	- g\*: 向下搜索匹配的单词，不必严格匹配单词（例如 foo 亦可搜索到 acfoosd）
	- \#: 向上搜索匹配的单词
	- g\#: 向上搜索匹配的单词，不必严格匹配单词


在 normal 模式下按下 `/` 即可进入查找模式，输入要查找的字符串并按下回车。 Vim 会跳转到第一个匹配。按下 `n` 查找下一个，按下 `N` 查找上一个。在查找模式中加入 `\c` 表示大小写不敏感查找，`\C` 表示大小写敏感查找。例如：`/foo\c` 将会查找所有的 `"foo"`, `"FOO"`, `"Foo"` 等字符串。
按下 `?` 也是进入查找模式，不过方向和 `/` 相反，也就是按下 `n` 查找上一个，`N` 查找下一个。

这些查找都是匹配对应串，而不是严格的单词。在查找的时候光标会跳跃，查找解释以及查找过程中都可以按
- "ctrl o": 返回光标上一个位置
- "ctrl i": 前进到光标下一个位置

按下 `q/` 搜索查找历史，选择一项后按下确定键，就以 `/` 模式来查找该项。
按下 `q?` 搜索查找历史，选择一项后按下确定键，就以 `?` 模式来查找该项。

### 替换
替换命令语法如下：`:{作用范围}s/{目标}/{替换}/{替换标志}`，例如 `:%s/foo/bar/g` 会在全局范围(`%`)查找 `foo` 并替换为 `bar`，所有出现都会被替换（`g`）。

- 作用范围：作用范围分为当前行、全部行、选区等等。
	- 当前行：`:s/foo/bar/g`
	- 全部行：`:%s/foo/bar/g`
	- 选区：这是 visual 模式下的操作，此模式下选择好范围后，按下 `:`，Vim 即可自动补全为 `:'<,'>`输入：`:'<,'>s/foo/bar/g` 即可。
	- 绝对特定行： `:5,12s/foo/bar/g` 5 到 12 行。
	- 相对特定行：相对行有 `.$+-` 四种符号可用，分别表示当前行，最后一行，以及相对当前行的加减。例如 `:.,+2s/foo/bar/g` 当前行和接下来的两行
- 替换标志符：下面的符号可以搭配出现
	- `g`: 表示全局，有的话就是对作用范围中的行，每一行都进行全部替换，否则只替换每一行的第一次出现。
	- `i` 表示大小写不敏感查找 `:%s/foo/bar/i` = `:%s/foo\c/bar`。
	- `I` 表示大小写敏感查找 `:%s/foo/bar/I ` = `:%s/foo\C/bar `。
	- `c` 表示需要确认。`:%s/foo/bar/gc`，回车后 Vim 会将光标移动到每一次 `"foo"` 出现的位置，并提示 `replace with bar (y/n/a/q/l/^E/^Y)?`，按下 `y` 表示替换，`n` 表示不替换，`a` 表示替换所有，`q` 表示退出查找模式， `l` 表示替换当前位置并退出。`^E` 与 `^Y` 也就是 `ctrl E ctrl Y`,控制屏幕按行移动。
	- `e`: 不显示错误

一些 tips：
1. 除了斜杠字符 `/`，还可以使用任何其他非字母数字字符作为分隔符。例如  `:%s#//#bar#g`。该命令将会在整个文件搜索 `//` 字符串，并替换 bar。这样就可以不必对 `/` 进行转义。
2. 替代命令将模式查找为字符串，而不是整个单词。要搜索整个单词，可以使用 `\<` 标记单词的开头，`\>` 标记单词的结尾。例如，要搜索 `foo` 一词，您可以使用 `\<foo\>`。`: s/\<foo\>/bar/`。
3. 可以查看替换的历史。这使用的是命令行本身的特性来查看。输入 `:s` 后，按上下键，就可以查看之前的输入的以· `:s` 开头的命令；输入 `:%s` 也是一样的道理。
4. `/\v` 进行魔法搜索模式，使得一些像*+等符号可以不需要转义。

一些常用的实例：
1. `:%s/\s+$//e` 这是一个非常有用的命令，它将在整个文件中搜索末尾的空白符并删除。`\s` 是正则表达式，匹配所有空白符，`+` 表示一个或者多个，`$` 表示行尾。
2. `:5,20s/^/#/` 该命令将在 5 到 20 行前添加 `#`，这相当与为注释 shell 脚本的 5-20 行的代码。如果需要取消第 5 行到第 20 行的注释，恢复之前的更改，运行命令 `:5,20s/^#//`。
3. `:%s/apple|orange|mango/fruit/g` 命令将会在整个文件搜索 `apple`，`orange` 和 `mango`，然后使用 `fruit` 替换与模式匹配的项。

## Global 命令
`:global`全局命令，通常简写为`:g`，可以针对匹配模式的行执行编辑命令；通过与正则表达式、寄存器和标记等功能相配合，可以高效地实现复杂的操作。使用`:g`命令，可以针对所有匹配模式的行执行操作。其命令格式为：

`:[range]g/{pattern}/[command]`

即针对在\[range]范围内，所有匹配{pattern}模式的行，执行\[command]命令。

命令`:g!`及其同义词`:v`，则可以针对所有不匹配模式的行执行操作。其命令格式为：

`:[range]g!/{pattern}/[command]`

即针对在\[range]范围内，所有不匹配{pattern}条件的行，执行\[command]命令。

如果没有指定\[range]，则针对文件中的所有行执行命令。也可以使用行地址，把全局搜索限定在指定的行或行范围内。

如果没有指定\[command]，则执行`:print`命令来显示行内容。

- 全局查找
	- `:g/pattern`：查找并显示文件中所有包含模式pattern的行，并移动到最后一个匹配处
	- `:g/pattern/p`：查找并显示文件中所有包含模式pattern的行
	- `:g/\<pattern\>/p`：查找并显示文件中所有精确匹配单词pattern的行
	- `:g!/pattern/nu`：查找并显示文件中所有不包括模式pattern的行，并显示这些行号：
- 全局删除
	- `:g/pattern/d`：删除包含模式patternn的行
	- `:g!/pattern/d`：删除不包含模式pattern的行
	- `:g/^$/d`：删除所有空行
	- `:g/^[ tab]*$/d`：删除所有空行以及仅包含空格和Tab制表符的行：
	- `:g/A/,/B/-1d`：这个命令中，范围是第一个包含 A 的行到包含B的行的前一行（-1）之间的全部行，命令是删除
	- 对于`A/,B/-1`这样的{pattern},会从文件开始寻找，找到了第一个包含A的行后，接着去寻找B。找到B后，接着循环继续找，直到文件末尾。选中所有匹配的块。
- 全局替换
	- `:g/A/s/B/C/`：将包含 A 的全部行里面的 B 替换成 C
	- `:g/A/-2,/A/+2s/B/C/c`：将包含A的行，选中其前两行到后两行的范围，把 B 替换成 C, 需要逐个进行确认
- 全局移动
	- `:g/.*/m0`：将所有的行按相反的顺序排列。其中，查找模式\.\*将匹配所有行，m0命令将每一行移动到0行之后
	- `:'a,'bg/^/m'b`：将指定标记之间的行按相反的顺序排列。（这说明了标记也能作为范围行选中）
	- `:g/A/,/B/-1m/C/-1`：将包含A的那一行，到下一个包含B的前一行，移动到包含C的前一行之前
- 全局复制
	- `:g/^/t.`：匹配到所有行，每一行进行重复
	- `:g/^/t$`：整体重复一次


## 正则表达式：
1. 元字符
    
    - 元字符
        
        |元字符|说明|
        |:-:|:--|
        |.|匹配任意字符|
        |[abc]|匹配方括号中的任意一个字符，可用`-`表示字符范围。如[a-z0-9]匹配小写字母和数字|
        |[^abc]|匹配除方括号中字符之外的任意字符|
        |\d|匹配阿拉伯数字，等同于[0-9]|
        |\D|匹配阿拉伯数字之外的任意字符，等同于[^0-9]|
        |\x|匹配十六进制数字，等同于[0-9A-Fa-f]|
        |\X|匹配十六进制数字之外的任意字符，等同于[^0-9A-Fa-f]|
        |\l|匹配[a-z]|
        |\L|匹配[^a-z]|
        |\u|匹配[A-Z]|
        |\U|匹配[^A-Z]|
        |\w|匹配单词字母，等同于[0-9A-Za-z_]|
        |\W|匹配单词字母之外的任意字符，等同于[^0-9A-Za-z_]|
        |\t|匹配`<TAB>`字符|
        |\s|匹配空白字符，等同于[\t]|
        |\S|匹配非空白字符，等同于[^\t]|
        
          
        
    - 一些普通字符需转义
        
        |元字符|说明|
        |:-:|:--|
        | `\*`|匹配 `*` 字符|
        | `\.`|匹配 `. ` 字符|
        | `\/`|匹配 ` / ` 字符|
        | `\\` |匹配 `\` 字符|
        | `\[`|匹配 ` [` 字符|
        | `\]`| 匹配 `] ` 字符|
        
          
        
    - 表示数量的元字符
        
        |元字符|说明|
        |:-:|:--|
        | `*`|匹配0-任意个|
        |`\+`|匹配1-任意个|
        |`\?`|匹配0-1个|
        |`\{n,m}`|匹配n-m个|
        |`\{n}`|匹配n个|
        |`\{n,}`|匹配n-任意个|
        |`\{,m}`|匹配0-m个|
        
          
        
    - 表示位置的元字符
        
        |元字符|说明|
        |:-:|:--|
        |$|匹配行尾|
        |^|匹配行首|
        | `\<`|匹配单词词首|
        | `\>`|匹配单词词尾|
        
          
        
2. 替换变量
    
    在正则式中以 `\(` 和 `\)` 括起来的正则表达式，在后面使用的时候可以用 `\1`、`\2` 等变量来访问 `\(` 和 `\)` 中的内容。（见下面的例子 6）


1. 删除行尾空格：`:%s/\s+$//g`
2. 删除行首多余空格：`%s/^\s*//` 或者 `%s/^ *//`
3. 删除沒有內容的空行：`%s/^$//` 或者 `g/^$/d`
4. 删除包含有空格组成的空行：`%s/^\s*$//` 或者 `g/^\s*$/d`
5. 删除以空格或TAB开头到结尾的空行：`%s/^[ |\t]*$//` 或者 `g/^[ |\t]*$/d`
6. 把文中的所有字符串“abc……xyz”替换为“xyz……abc”可以有下列写法
```
	:%s/abc\(.*\)xyz/xyz\1abc/g
    :%s/\(abc\)\(.*\)\(xyz\)/\3\2\1/g
```




## Neovim中设置的快捷键


- `<Aenter>` 全屏
- `<leader>u`: 展示undo history
- Lazy:
	- `<leader>p`: 打开 lazyvim 相关命令，常用 `s`。
- 文件树
	- `<Cn>`: 打开文件树
	- `q`：关闭文件树
	- `<leader>n`: 打开文件树相关命令。有刷新等
- 终端：
	- `<A\>`: 右边打开终端
	- `<C\>`: 下边打开终端
	- `<Ad>`: 悬浮打开终端
	- `<Cd>`：退出终端（清除终端，下次打开就新建）
- 多窗口：每个标签是一个文件（buffer）, 子窗口选择 buffer 来显示
	- `<Anum>`：num 是数字，切换 buffer,（效果像切换标签）
	- `<A(jk)`: 右左切换标签
	- `<AS(jk)>`：将当前标签向右左移动
	- `<Aq>`: 关闭当前窗口内的 buffer ，窗口不关闭。会提示是否进行保存
	- `<ASq>`：关闭当前窗口，buffer 不关闭（q!）
	- `<Cq>`: 关闭此窗口，buffer 不关闭 (wq) 这个命令比上面的更安全
	- `<leader>be`：按照文件后缀名来排序 buffer
	- `<leader>bd`：按照文件的目录来排序 buffer
	- `<C(hjkl)>`：转换窗口浏览
	- `<Cw>v`: 水平分窗
	- `<Cw>s`: 垂直分窗
- 光标在 nvim-tree 树中：
	- `a`：创建新文件
	- `d`：删除该文件
	- `r`：重命名
	- `y`：复制文件名
	- `Y`：复制相对路径
	- `gy`：复制绝对路径
	- `H`：显示/隐藏以 `.` 开头的文件和文件夹
	- `I`：显示/隐藏在 nvim-tree 插件中配置隐藏的文件和文件夹
	- `Enter` ：打开文件或展开文件夹
	- `Backspace`：折叠文件夹（对着文件夹下面的文件可以直接按，不用对着文件夹按）
	- `C-k`: 显示文件信息
- Telescope Operations：
	- `<leader>fr`：基于频率打开最近文件表
	- `<leader>fw`：在当前项目查找 word
	- `<leader>ff`：在当前项目中寻找文件
	- `<leader>fb`：查看当前 buffer
	- `<leader>fs`：查找当前光标指到的 word
	- `<leader>fd`：搜索已记录的会话（不用选择开始的文件）
	- `<leader>fe`：按照历史记录搜索
	- `<leader>fz` : 寻找 zoxide 记录的文件夹
	- `<leader>fp`：寻找项目
	- `<leader>fc`：为当前会话改变主题
	- `<leader>fn`：新建文件
	- `<leader>fg`：查看当前文件夹中，被 git 追踪的文件
	- `gto`：列出当前文件的函数类等
- Vanilla vim operations：
	- `jk`：插入模式退出到正常模式
	- `<S-Tab>`: 折叠当前块
弹出的两个窗口大多可以按 `[]` 进行切换和 `q`, `Esc` 退出。
- Lsp operations by `lspsaga.nvim`：大部分窗口可以按 `q` 和 `Esc` 退出
	- `ga`：code action，为当前行给出一些可能的改进动作
	- `gd`：预览定义
	- `gD`：跳转到定义
	- `go`：列出当前文件的函数类等
	- `gr`：在当前文件给函数变量等重命名
		- `<Ctrl c>`: 退出
	- `gR`：在当前项目给函数变量重命名
	- `gh`：光标指向函数名，悬浮窗口给出函数定义及使用的地方
	- `gs`：光标指向函数内部（括号内），悬浮窗口给出函数的签名（输入输出类型）
	- `<leader>ci`：找到使用了这个函数的函数
		此快捷键和下一个打开的窗口有下面的命令：
		- `e`：编辑该窗口
		- `v`：水平打开此文件
		- `s`：垂直打开
		- `t`：新标签中打开
		- `q` 和 `Esc`：退出
		- `[]`：在打开的两个窗口切换
	- `<leader>co`：找到这个函数使用的函数（未成功）
	- `K`：显示函数类等的文档
	- `<leader>ld`：诊断当前行
- Lsp operations by `trouble.nvim`：
	- `gt`：以表形式在下方列出诊断（当前 buffer 全部）
	- `<leader>tr`：以表形式在下方列出引用
	- `<leader>td`：以表形式在下方列出当前文件的诊断
	- `<leader>tw`：以表形式在下方列出全部 buffer 的诊断
	- `<leader>tq`：以表形式在下方列出 quick fix. (空)
	- `<leader>tl`：以表形式在下方列出 local list（空）
- Code completion：
	- `Tab`：向下选择补全
	- `<S Tab>`：向上选择
	- `<C w>`：关部补全窗口
- 区域跳转
	- `<A l>`：insert 模式，如果光标在 (){}""中，则跳到这个末尾 },},"
	- `<A h>`：insert 模式，同上，跳到前面
- Code selection：
	- `vab`：选中当前所在 ()的内容
	- `vib`：选中当前 ()内容，不包括括号
	- `vaB`：选中当前所在 {} 的内容
	- `viB`:：选中当前所在{内容，不包括括号}
	- `vaf`：选中当前所在函数块，包括函数名和{}
	- `vif`：选中当前函数块，不包括函数名和{}
	- `vac`：选中当前所在类，包括类名和{}
	- `vic`：选中当前所在类，不包括类名和{}
- run:
	- `<leader>r`：全局执行，或是对选中的代码块执行
- 光标跳转：
	- `<leader>j` 和 `<leader>k`：选则行跳转
	- `<leader>w`：选中单词跳转
	- `<leader>c`：选中一个字符跳转
	- `<leader>cc`：选中两个字符跳转
	- `Fx`：往前寻找字符 x，可以配合 `;` 和 `,` 进行寻找
	- `fx`：往后寻找字符 x，可以配合 `;` 和 `,` 进行寻找
	- `]]`：去下一个函数的尾部
	- `][`：去下一个函数的头部
	- `[[`：去上一个函数的头部
	- `[]`：去上一个函数的尾部
	- `g]`：去下一个诊断问题
	- `g[`：去上一个诊断问题
	- `[g`：去上一个未暂存 (git)的行
	- `]g`：去下一个未暂存的行
- Code comment:
	- `gcc`：注释当前行“//”
	- `gc`：在 visual 模式下注释选定行，normal 模式下后面接光标移动符号，比如 `gc3j`。
	- `gbc`：块状注释当前行
	- `gb`：块注释（/\*\*/）,  visual 模式下注释选定行, normal 模式下后面接光标移动符号
- Session management:
	- `<leader>ss`: 保存此会话
	- `<leader>sl`：加载当前目录的会话
	- `<leader>sd`：删除当前会话
- Git management：
	- `<leader>g`：打开 lazygit
	- `<leader>G`: 打开 vim-fugitive，查看状态
	- `<leader>D`：打开 diff view，查看改变
	- `<leader><leader>D`：退出上面命令打开的视窗
	- `<leader>hs`：将当前行所在的未暂存的 hunk 进行暂存
	- `<leader>hu`：取消最近一次暂存
	- `<leader>hr`：清除当前行所在的未暂存块
	- `<leader>hR`：清除当前文件全部的为暂存块
	- `<leader>hp`：预览当前未暂存的块
	- `<leader>hb`：查看当前块的 git 信息 (commit 的具体信息或者显示未提交)
	- `gps`：git push
	- `gpl`：git pull
- Debug mode(supports c&cpp&rust&golang&python now)
	- `F6`：开始 debug 或继续
	- `K`：debug 模式下，显示变量当前的值
	- `F7`：断开与目标进程的连接，但不会结束目标进程的运行
	- `F8`：断点
	- `F9`：步入，进入当前行函数或方法，若没有函数或方法，和 `f11` 一样
	- `F10`：步出：退出当前的函数或方法
	- `F11`：步过：执行当前行的代码，但是不会进入任何函数或方法。如果当前行有函数或方法调用，那么这些函数或方法会被完整地执行
	- `<leader>dl`：重新 debug 上一次会话 (断点等都不需要重新配置)
	- `<leader>dc`：执行到当前光标。（这一行还未执行）
	- `<leader>db`：设置具有条件的断点
	- `<leader>do`：打开REPL
- corekey:
	- `<A[>` 和 `<A]>`：水平方向调整窗口大小
	- `A;` 和 `A'`：垂直方向调整窗口大小
	- `>>` 和 `<<`：右或左缩进当前行
	- `>`: `<`：在 visual 模式下，右或左缩进选中行
	- `J` 和 `K`: visual 模式下，上下移动行
	- `<C-q>`: wq，在 insert 模式下也可用
	- `<A-S-q>`: q!
	- `<C-s>`: w，在 insert 模式下也可用
	- `<A-s>`：使用 sudo 来保存文件
	- `<C-a>`：insert 模式下移动到行首
	- `F12`：预览 markdown 文件
