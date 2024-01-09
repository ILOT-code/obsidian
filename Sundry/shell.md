# 常用命令

## ls
通过 ls 命令不仅可以查看 linux 文件夹包含的文件，而且可以查看文件权限(包括目录、文件夹、文件权限)、查看目录信息等等。
```
ls [opt] [dirname]
```

```
ls -a 列出目录所有文件，包含以.开始的隐藏文件
ls -A 列出除.及..的其它文件
ls -l 除了文件名之外，还将文件的权限、所有者、文件大小等信息详细列出来
ls -R 同时列出所有子目录层
ls -h 以易读大小显示(GB,MB)
ls -r 反序排列
ls -t 以文件修改时间排序
ls -S 以文件大小排序
```

可以使用正则式：
```
ls -l t*   列出当前目录下以t开头的文件及目录
```
## cd
```
cd [diename]
```

```
cd ~  到用户目录下
cd /  到根目录
cd .. 到上级目录
cd -  返回进入此目录之前所在的目录
cd !$ 把上个命令的参数作为cd参数使用
```
## pwd
```
pwd     查看当前路径
pwd -P  查看软链接的实际路径
```
## mkdir
```
mkdir [opt] [dir]
```

创建目录
```
-m 对新建目录设置存取权限，也可以用 chmod 命令设置
-p 此时若路径中的某些目录尚不存在,加上此选项后，系统将自动建立好那些尚不在的目录，即一次可以建立多个目录
-v 显示创建信息
```

```
mkdir -p ./test/t  同时创建test和其下的t目录
mkdir -m 777 test  创建权限777的目录
```
## rm
```
rm [opt] [path]
```

删除文件或文件夹
```
-f 强制删除，从不给出提示
-r 递归删除路径下的目录和文件
-i 进行交互式删除
-v 显示删除信息
```

可以配合正则式
```
rm -i *.log 删除目录下 .log 文件，删除前逐一询问确认
```
## rmdir
```
rmdir [opt] [dirpath]
```

删除空目录

```
-p 后面接一个嵌套的目录路径，如果该目录删除后，其父目录为空，那么一并删除它
-v 显示信息
```

```
rmdir -p test/t 删除空的目录test/t，如果在这之后test为空，那么一并删除它
```
## mv
```
mv [opt] [dir|file] [dir|file]
```

文件的重命名或文件、目录的移动
```
-b 若需覆盖文件，则覆盖前先行备份
-f 强制覆盖
-i 询问覆盖
-u 若目标文件已经存在，且 source 比较新，才会更新
-t 即指定mv的目标目录，该选项适用于移动多个源文件到一个目录的情况，此时目标目录在前，源文件在后。
```

```
mv 1.txt 2.txt         改名
mv 1.txt 2.txt /test   移动
mv dir1 dir2           目录移动
mv * ../               将当前目录文件按移到上一级
mv dir1/*.txt dir2     配合正则表达式
```
## cp
```
cp [opt] [file] [dir|file]
```

将源文件复制至目标文件，或将多个源文件复制至目标目录。
```
-i 询问是否覆盖
-a 新的文件会保持和源文件相同的时间
-r 复制目录及目录内的所有项目
-f 如果目标文件无法打开则将其移除并重试
-s 建立连接（快捷方式）
```

```
cp -r dir1 dir2        复制整个目录
cp -s 1.log 1_link.log 创建一个快捷方式
```
## cat
```
cat [opt] [file]
```
连接文件或标准输入并打印。这个命令常用来显示文件内容，或者将几个文件连接起来显示，或者从标准输入读取内容并显示，它常与重定向符号配合使用。
```
-A
-b 对非空行进行编号，空行不编号
-n 对所有行编号
```

```
cat file   显示整个文件内容
cat > file 从键盘创建一个新文件或复写该文件，ctrl-d 来结束
cat > file <<EOF 同上，输入EOF结束
cat >> file 追加到该文件
cat file1 file2 > file3 合并文件
cat -b file1 > file2 文件1编号后写入文件2
```

## touch
```
touch file  创建不存在的文件
touch file1 file2 创建file1,和file2一模一样
touch -r file1 file2 更爱file1的时间戳和file2一样
touch -t 201910102034.50 file 设定时间戳
```
## more
```
more [-dlfpcsu ] [-num ] [+/ pattern] [+ linenum] [file ... ]
```

显示文件内容
```
-d  显示按键提示
-c  从顶部清屏，然后显示
-p  和-c类似
-s  把连续的多个空行显示为一行
-u  把文件内容的下划线去掉
+/pattern 在每个档案显示前搜寻该字串(pattern)，然后从该字串前两行之后开始显示
+n  从第n行开始显示
-n  定义每次显示数量是n行（之后的翻页操作就会变成n行）
```

操作
```
Enter  向下n行，需要定义。默认为1行
Space 和 f 向下滚动一屏(n行)
b  向上一屏
=      输出当前行号
:f     输出文件名和当前行的行号
v      调用vim编辑器
q      退出
```

```
ls -l | more -5  列出文件，每屏显示5行
```
## less

## head
```
head [opt] [file]
```

显示文件头部
```
-q 隐藏文件名
-v 显示文件名
-c [n] 显示的字节数，[n]可以为负数，表示最后几个字节
-n [n] 显示的行数，[n]可以为负数，表示最后几行
```
## tail
```
tail [opt] [file]
```

查看尾部
```
-f 循环读取，适用于递增的文件
-c [n] 字节数
-n [n] 行数
```

```
ping 127.0.0.1 > ping.log &  移动到后台运行
tail -f ping.log  查看上面那个递增的文件
```

可以使用 `jobs` 查看后台运行的文件，并使用 `fg` 移到前台。
## which
查找某个系统命令的位置，并返回第一个搜索结果
```
which ls
```
## whereis
```
-b   定位可执行文件。
-m   定位帮助文件。
-s   定位源代码文件。
-u   搜索默认路径下除可执行文件、源代码文件、帮助文件以外的其它文件。
```

```
whereis git
whereis -b python
```
## locate
locate 通过搜寻系统内建文档数据库达到快速找到档案，数据库由 updatedb 程序来更新，updatedb 是由 cron daemon 周期性调用的。默认情况下 locate 命令在搜寻数据库时比由整个由硬盘资料来搜寻资料来得快，但较差劲的是 locate 所找到的档案若是最近才建立或刚更名的，可能会找不到.
另外，这个命令需要安装才能用。

## find
```
find path -opts [-print| -exec| -ok ...]
```

参数意义
```
path: find命令所查找的目录路径。例如用.来表示当前目录，用/来表示系统根目录。
-print： find命令将匹配的文件输出到标准输出。
-exec： find命令对匹配的文件执行该参数所给出的shell命令。相应命令的形式为'command' {  } \;，注意{   }和\；之间的空格。
-ok： 和-exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的shell命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行
```

opt 选项：
```
-name 按照文件名查找文件，后面如果要接正则式需要加''
-perm 按文件权限查找文件
-user 按文件属主查找文件
-group  按照文件所属的组来查找文件。
-type  查找某一类型的文件，诸如：
   b - 块设备文件
   d - 目录
   c - 字符设备文件
   l - 符号链接文件
   p - 管道文件
   f - 普通文件

-size [+-]n[c] 找文件长度为n块文件，带有c时表文件字节大小,+表示大于，-表示小于,不加表示等于
-amin n   查找系统中最后N分钟访问的文件
-atime n  查找系统中最后n*24小时访问的文件
-cmin n   查找系统中最后N分钟被改变文件状态的文件
-ctime n  查找系统中最后n*24小时被改变文件状态的文件
-mmin n   查找系统中最后N分钟被改变文件数据的文件
-mtime n  查找系统中最后n*24小时被改变文件数据的文件
(用减号-来限定更改时间在距今n日以内的文件，而用加号+来限定更改时间在距今n日以前的文件。 )
-maxdepth n 最大查找目录深度
-prune 选项来指出需要忽略的目录。在使用-prune选项时要当心，因为如果你同时使用了-depth选项，那么-prune选项就会被find命令忽略
-newer 如果希望查找更改时间比某个文件新但比另一个文件旧的所有文件，可以使用-newer选项
```

```
find -atime -2            查找当前目录 48 小时内修改过的文件
find ./ -name '*.log'     在当前目录查找 以 .log 结尾的文件。 . 代表当前目录
find /opt -perm 777       查找 /opt 目录下 权限为 777 的文件
find -size +1000c         查找大于 1000字节 的文件
find -size 1000c          查找等于 1000字节的文件

查找当前目录中以一个小写字母开头，最后是 4 到 9 加上 .log 结束的文件：
find . -name '[a-z]*[4-9].log' -print

在 test 目录查找不在 test4 子目录查找
find test -path 'test/test4' -prune -o -print

查找更改时间比文件 log2012.log新但比文件 log2017.log 旧的文件
find -newer log2012.log ! -newer log2017.log


depth 选项可以使 find 命令向磁带上备份文件系统时，希望首先备份所有的文件，其次再备份子目录中的文件。
find 命令从文件系统的根目录开始，查找一个名为 CON.FILE 的文件。 它将首先匹配所有的文件然后再进入子目录中查找
find / -name "CON.FILE" -depth -print
```

-exec 参数后面跟的是 command 命令，它的终止是以 ; 为结束标志的，所以这句命令后面的分号是不可缺少的，考虑到各个系统中分号会有不同的意义，所以前面加反斜杠。{} 花括号代表前面 find 查找出来的文件名。

```
在当前目录中查找更改时间在10日以前的文件并删除它们(无提醒）：
find . -type f -mtime +10 -exec rm -f {} \;

当前目录中查找所有文件名以.log结尾、更改时间在5日以上的文件，并删除它们，只不过在删除之前先给出提示。 按y键删除文件，按n键不删除：
find . -name '*.log' -mtime +5 -ok rm {} \;

当前目录下查找文件名以 passwd 开头，内容包含 "pkg" 字符的文件
find . -f -name 'passwd*' -exec grep "pkg" {} \;

用 exec 选项执行 cp 命令
find . -name '*.log' -exec cp {} test3 \;
```

xargs：find 命令把匹配到的文件传递给 xargs 命令，而 xargs 命令每次只获取一部分文件而不是全部，不像 -exec 选项那样。这样它可以先处理最先获取的一部分文件，然后是下一批，并如此继续下去。另外，xargs 可以指定-i 参数，将 find 的输出用{}替代，也可以使用参数-I 来指定用什么替代。-p参数会提示确认是否执行后面的命令,y执行，n不执行。

```
查找当前目录下每个普通文件，然后使用 xargs 来判断文件类型（file是一个命令，可以查看文件类型）
find . -type f -print | xargs file

查找当前目录下所有以 js 结尾的并且其中包含 'editor' 字符的普通文件
find . -type f -name "*.js" -exec grep -lF 'ueditor' {} \;
find -type f -name '*.js' | xargs grep -lF 'editor'

利用 xargs 执行 mv 命令
find . -name "*.log" | xargs -i mv {} dir

用 grep 命令在当前目录下的所有普通文件中搜索 hostnames 这个词，并标出所在行：
find . -name \*(转义） -type f -print | xargs grep -n 'hostnames'

将txt文件复制到父目录
find . -name "*.txt" | xargs -I [] cp [] ..

需要确认的执行
find . -name "*.log" | xargs -p -i mv {} ..

在整个系统中查找内存信息转储文件(core dump) ，然后把结果保存到 tmpcore.log 文件中
find / -name "core" -print | xargs echo "" > /home/yiibai/tmpcore.log

```
## tar

```
tar [opt] [file]
```

用来压缩和解压文件。tar 本身不具有压缩功能，只具有打包功能，有关压缩及解压是调用其它的功能来完成。
弄清两个概念：打包和压缩。打包是指将一大堆文件或目录变成一个总的文件；压缩则是将一个大的文件通过一些压缩算法变成一个小文件
```
-c 建立新的压缩文件
-f 指定压缩文件
-r 添加文件到已经压缩文件包中
-u 添加改了和现有的文件到压缩包中
-x 解开压缩包
-t 显示压缩文件中的内容
-z 支持gzip压缩
-j 支持bzip2压缩
-Z 支持compress解压文件
-v 显示操作过程
```

单纯的 tar:
```
这条命令是将所有 .jpg 的文件打成一个名为 all.tar 的包。-c 是表示产生新的包，-f 指定包的文件名。
tar -cf all.tar *.jpg

这条命令是将所有 .gif 的文件增加到 all.tar 的包里面去，-r 是表示增加文件的意思。
tar -rf all.tar *.gif

这条命令是更新原来 tar 包 all.tar 中 logo.gif 文件，-u 是表示更新文件的意思。
tar -uf all.tar logo.gif

这条命令是列出 all.tar 包中所有文件，-t 是列出文件的意思。
tar -tf all.tar

这条命令是解出 all.tar 包中所有文件，-x 是解开的意思。
tar -xf all.tar
```

使用 gzip 压缩和 gunzip 解压，适用 .tar.gz 后缀的文件
```
打包后压缩
tar -zcf all.tar.gz *.jpg

解压并解包
tar -zxf all.tar.gz

查看
tar -ztvf all.tar.gz

要压缩打包 /home, /etc ，但不要 /home/dmtsai
tar --exclude /home/dmtsai -zcvf myfile.tar.gz /home/* /etc
```

使用 bzip2 压缩和 bunzip2 解压，bzip2 是一个压缩能力更强的压缩程序。适用 tar. bz2 后缀的文件
```
tar -jcf all.tar.bz2 *.jpg
tar -jxf all.tar.bz2
```

使用 compress，适用 tar.Z 后缀的文件
```
压缩与解压
tar -Zcf all.tar.Z *.jpg
tar -Zxf all.tar.Z
```

单纯使用 gzip 和 gunzip，适用.gz 后缀的文件。这种压缩方式不能保存原文件，且不能压缩文件夹
```
解压1：gunzip FileName.gz 
解压2：gzip -d FileName.gz 
压缩：gzip FileName
```

单纯使用 bzip2 和 bunzip2, 适用.bz2 后缀的文件。
```
解压1：bzip2 -d FileName.bz2 
解压2：bunzip2 FileName.bz2 
压缩： bzip2 -k FileName      -k参数指明要保留原文件
```

使用 zip 压缩，适用 zip 后缀文件。可以压缩目录
```
压缩和解压文件
zip boduo.zip boduo
unzip boduo.zip

压缩和解压目录
zip -r Demo.zip Demo
unzip Demo.zip
```
## chmod

```
chmod [opt] mod file
```

更改权限。

参数：
```
-c 当发生改变时，报告处理信息
-R 处理指定目录以及其子目录下所有文件
-f 错误信息不输出
-v 运行时显示详细处理信息
```

权限范围：
```
u ：目录或者文件的当前的用户
g ：目录或者文件的当前的群组
o ：除了目录或者文件的当前用户或群组之外的用户或者群组
a ：所有的用户及群组
```

权限代号：
```
r ：读权限，用数字4表示
w ：写权限，用数字2表示
x ：执行权限，用数字1表示
- ：删除权限，用数字0表示
s ：特殊权限
```

```
增加文件 t.log 所有用户可执行权限
chmod a+x t.log

重新定义所有者的权限,并输出处理信息
chmod -c u=r t.log

给 file 的属主分配读、写、执行(7)的权限，给file的所在组分配读、执行(5)的权限，给其他用户分配执行(1)的权限
chmod -c 751 t.log （或者：chmod u=rwx,g=rx,o=x t.log -c)

给属主和组增加写权限，其它撤销执行权限
chmod ug+w,o-x run.py

将 test 目录及其子目录所有文件添加可读权限
chmod -R -c u+r,g+r,o+r text/
```
## chown
```
chown [opt] [owner][:[group]] file
```

将指定文件的拥有者改为指定的用户或组，用户可以是用户名或者用户 ID；组可以是组名或者组 ID；文件是以空格分开的要改变权限的文件列表，支持通配符.
```
-c 显示更改的部分的信息
-f 忽略错误信息
-h 修复符号链接
-R 处理指定目录以及其子目录下的所有文件
-v 显示详细的处理信息
-deference 作用于符号链接的指向，而不是链接文件本身
```

```
改变文件的拥有者是yiibai，组是root
chown yiibai:root newfile.txt

文件拥有者和组都改成了root
chown root: newfile.txt

只改变了组
chown :yiibai newfile.txt

递归目录的文件，改变拥有者和组
chown -R -v root:yiibai scripts/
```

## grep

```
grep [option] pattern file|dir
```

强大的文本搜索命令，grep(Global Regular Expression Print) 全局正则表达式搜索。
grep 的工作方式是这样的，它在一个或多个文件中搜索字符串模板。如果模板包括空格，则必须被引用，模板后的所有字符串被看作文件名。搜索的结果被送到标准输出，不影响原文件内容。

```
-A n --after-context显示匹配字符后n行
-B n --before-context显示匹配字符前n行
-C n --context 显示匹配字符前后n行
-c --count 计算符合样式的列数
-i 忽略大小写
-l 只列出文件内容符合指定的样式的文件名称
-L 只列出文件内容不符合指定的样式的文件名称
-f 从文件中读取关键词
-n 显示匹配内容的所在文件中行数
-R 递归查找文件夹
```

正则表达式规则
```
^  #锚定行的开始 如：'^grep'匹配所有以grep开头的行。 
$  #锚定行的结束 如：'grep$'匹配所有以grep结尾的行。 
.  #匹配一个非换行符的字符 如：'gr.p'匹配gr后接一个任意字符，然后是p。  
*  #匹配零个或多个先前字符 如：'*grep'匹配所有一个或多个空格后紧跟grep的行。
.*   #一起用代表任意字符。  
[]   #匹配一个指定范围内的字符，如'[Gg]rep'匹配Grep和grep。 
[^]  #匹配一个不在指定范围内的字符，如：'[^A-FH-Z]rep'匹配不包含A-R和T-Z的一个字母开头，紧跟rep的行。  
\(..\)  #标记匹配字符，如'\(love\)'，love被标记为1。   
\<      #锚定单词的开始，如:'\<grep'匹配包含以grep开头的单词的行。
\>      #锚定单词的结束，如'grep\>'匹配包含以grep结尾的单词的行。
x\{m\}  #重复字符x，m次，如：'0\{5\}'匹配包含5个o的行。 
x\{m,\}  #重复字符x,至少m次，如：'o\{5,\}'匹配至少有5个o的行。  
x\{m,n\}  #重复字符x，至少m次，不多于n次，如：'o\{5,10\}'匹配5--10个o的行。  
\w    #匹配文字和数字字符，也就是[A-Za-z0-9]，如：'G\w*p'匹配以G后跟零个或多个文字或数字字符，然后是p。  
\W    #\w的反置形式，匹配一个或多个非单词字符，如点号句号等。  
\b    #单词锁定符，如: '\bgrep\b'只匹配grep。
```

```
查找指定进程
ps -ef | grep svn

查找指定进程个数
ps -ef | grep svn -c

从文件中读取关键词
cat test1.txt | grep -f key.log

从文件夹中递归查找以grep开头的行，并只列出文件
grep -lR '^grep' /tmp

查找非x开头的行内容
grep '^[^x]' test.txt

显示包含 ed 或者 at 字符的内容行
grep -E 'ed|at' test.txt
```
## df

## du

## ln

## date

## cal
## wc

## ps

## top

## kill

## free

## 