#Linux 命令学习

[TOC]

##文件与目录操作

###文件描述

描述信息一共有10位。 e.g. `drw-r--r--`

权限有：`r`读取； `w`写入； `x`执行

第一位，表示文件的类型：
 
 - `d` 表示目录
 - `l` 符号链接（软链接）
 - `-` 表示普通文件
 - `c` 字符设备文件
 - `b` 块设备文件

第2~4位：`u`文件所有者 所拥有的权限
第5~7位：`g`文件所有者所在组的用户 所拥有的权限
第8~10位：`o`其他用户 所拥有的权限

### ls 命令

 - 显示所有文件（包括隐藏文件） **-a 等同于 --all**
```bash
$ ls -a
```

 - 查看文件`索引节点号` inode
```bash
$ ls -i
```
如果两个文件对应的索引节点号相同，那么他们所对应的文件内容也相同。

### chgrp：change group
修改文件的工作组
```bash
#默认只改变目录的所有者，文件夹下面的文件的所有者不变。
$ chgrp targetGroup dir
```

```bash
#改变目录的所有者及其下面的文件 的所有者
$ chgrp -R targetGroup dir
```

>工作组有编号，root组为0
可以使用工作组编号代替组名！

###chmod 修改权限
权限有：`r`读取； `w`写入； `x`执行

`u`文件所有者
`g`文件所有者所在的组
`o`别的用户
`a`所有用户

 1. 使用等号的方式
```bash
$ chmod u=rwx,g=rw,o=r test.log
$ chmod a=r test.log
```
 2. 使用加减号的方式
```bash
$ chmod u+x test.log
$chmod o-r test.log
```
 3. 使用数字的方式来表示权限
rwx 分别对应 4,2,1
针对各个权限组，使用累加和来表示相应赋予的权限。
```bash
$ chmod 644 test.log
```

### find 查找
按照一定条件进行查找。

可用的选项：

|选项|含义|
|---|---|
|-name|按照文件名查找|
|-user|按照用户查找|
|-exec|针对找到的文件，进行操作|

e.g.
从根目录下查找，所有文件名为filename的文件
```bash
$　find / -name filename
```

`-exec`查找并调用外部命令，直接执行。

e.g.查找并删除。将查找结果放入 `{}`中，后面添加` \;`(不能少了空格)
```bash
$ find /home -name mytest -exec rm -f {} \;
```

### which 查找指令的路径

```bash
$ which ls
```

### locate 查找文件的保存路径
locate是基于数据库的操作。
首次调用需要创建locate数据库，比较慢。
查询的速度很快，比 find 快很多，但是是基于数据库，可能不是最新的信息。

更新locate数据库：
```bash
$ updatedb
```

### rename 批量重命名

>单个文件可以直接使用 `mv`命令重命名

针对批量重命名,使用rename。
e.g.
将符合 ’file*’ 条件的所有文件名中的 ’file’ 替换为 ’linux’
```bash
$ rename file linux file*
```

### file 查看文件类型
```bash
$ file test.log
```
### touch 创建空文件
功能：

 1. 改变文件的时间等属性
 2. 创建一个新的空文件 

```bash
#将文件时间修改为当前的时间
#如果文件不存在，直接创建空文件
$ touch test.log
```

批量创建空文件:
```bash
$ touch filename{1,2,3,4,5,6,7,8,9}
```
将创建9个文件，名字为 **filename+i**


### dd 复制文件并转换

在复制文件时候，进行类型转换等处理。

`if`输入文件
`of`输出文件
`conv`复制时候，进行的操作

```bash
#conv:转换成大写
$ dd if=test.log conv=ucase  of=newtest.log
```
```bash
#制作光盘的镜像文件
$ dd if=/dev/cdrom of=mycd.iso
```

###dirname 获取 带路径文件名的 路径

e.g.
```bash
#完成的路径：/etc/httpd/conf/httpd.conf
$ dirname /etc/httpd/conf/httpd.conf
```
返回 /etc/httpd/conf/

### basename 获取 带路径文件名的 文件名

e.g.
```bash
#完成的路径：/etc/httpd/conf/httpd.conf
$ basename /etc/httpd/conf/httpd.conf
```
返回 httpd.conf

进一步，去除文件名后缀(直接在后面添加需要删除的后缀,可任意设置)：
```bash
#完成的路径：/etc/httpd/conf/httpd.conf
$ basename /etc/httpd/conf/httpd.conf .conf
```

###pathchk 可移植性检测

###unlink 删除文件
无法删除目录
```bash
$ unlink test.log
```

### alias 定义命令别名

```bash
$ alias vi=vim
```

##文本编辑

### vi 文本编辑

vi占用整个屏幕，上面是内容显示，最下面一行是当前状态。

两种运行模式：

 1. 编辑模式
 2. 命令模式

打开时，默认是命令模式。


命令模式 -> 编辑模式 一共有六种方式：

 - `i` Insert 插入模式，在光标之前插入文本
 - `I` Insert 插入模式。默认在**行首**插入数据。
 - `o` 在当前行光标的下面一行插入数据
 - `O` 在当前行光标的上面一行插入数据
 - `a` Append 追加模式，在光标之后添加文本
 - `A` Append 追加模式，在**行尾**追加文本

编辑模式 -> 命令模式 ：
`Esc`


在命令模式下，执行退出：

 - `:wq`、`:x`、`shift+zz`保存并退出
 - `:q!`直接退出

在 命令模式 下，

 - 移动光标 方向键、`HJKL`
 - 切换到指定行 `:100`
 - 跳转到最后一行 `:$`、`shift+g`
 - 复制当前行 `yy`
 - 在当前行粘贴 `pp`
 - 删除当前行 `dd`
 - 撤销操作 `u`,undo
 - 搜索文本内容 `/content`
 
####vi编辑的设置
查看set选项：`:set`,显示所有可用的配置信息

 - 显示行号 `:set number`、`:set nu`,撤销显示行号`:set nonumber`、`:set nonu`
 - 使用缩进 `:set autoindent`

使用配置文件，永久保存配置信息：
```bash
#针对当前用户
$ vi ~/.vimrc

#针对所有用户
$ vi /etc/vimrc
```
写入
```
set number
set autoindent
```
保存退出即可。

### ed 行文本编辑器
避免操作大文件时，全部读取到内存。

### ex vi的单行编辑模式
可以使用所有vi指令。
e.g. `:100`选取指定行

### sed 流式编辑器

删除指定行：
```bash
#删除第一行
$ sed -e '1d' /etc/fstab
```

>源文件不变！只是在源文件流式输出的时候，删除第一行。

```bash
#删除1到4行
$ sed -e '1，4d' /etc/fstab
```

##文本过滤与处理


### cat 显示小文件内容
如果文件太长，内容会显示不全。所以，cat适合查看小文件。

`cat -s`可以合并文件中 多个空格 -> 一个空格
```bash
$ cat -s test.log
```

### more 分屏查看大文件内容
```bash
$ more test.sh
```

 - 换行      Enter回车
 - 分屏滚动   Space空格 

使用管道符号，分屏显示:
e.g. 分屏显示进程信息
```bash
$ ps aux | more 
```

### less 分屏查看

支持上下左右，调整查看文件的内容（more不支持）

文件的搜索：

 - `/str`查找str，此时按 `n`，下一个str；按`N`上一个str
 - `?str`从光标位置向前搜索str，也支持 n 和 N

可以在less中临时执行别的命令。
e.g. `!ls`在less中查看当前文件列表

### grep 行匹配指令，可用作查找
支持正则表达式。

在/etc/passwd文件中，查找包含root行
```bash
$ grep root /etc/passwd
```
在文件/etc/passwd中，搜索以 fs结尾 的行
```bash
$ grep  -n 'fs$' /etc/passwd
```

### head 显示文件前几行数据
默认是 10 行。
通过 `-n` 指定显示的行数。
```bash
$ head -n 15 test.log
```
可以同时指定多个文件，输出多个文件的前几行。

### tail 显示文件的最后的数据
默认是 10 行。
通过 `-n` 指定显示的行数。
```bash
$ tail -n 15 test.log
```

`-f`监控文件的变化-->可用来监控日志文件
```bash
$ tail -f test.log
```
此时，若 test.log被修改，则会显示尾部变化的数据。


### wc 统计文本文件信息

```bash
$ wc test.sh
```
返回 `行数/单词数/字符数`
```
   51      19     162 test.sh
```
只查看 行数`-l`：
```bash
$ wc -l test.sh
```
配合管道命令：
查看包含ghome进程的行数（个数）
```bash
$ ps aux | grep ghome | wc -l
```

### uniq 去除重复的行
只过滤输出，不修改源文件。
如果需要保存，可以使用`重定向`。

>前提：需要是经过排序的数据。

```bash
$ uniq test.sh
```
针对未排序的文件，首先进行排序，再进行uniq。
```bash
$ sort test.txt | uniq
```
统计重复行出现的次数`-c`
```bash
$ sort test.txt | uniq -c 
```

### cut 截取字段

 - `-f` 指定列
 - `-d` 指定分隔符
```bash
$ cut -f 1 -d " " /home/test.txt
```

 - `-c`显示前几列。获取前15个字符：
```bash
$ cut -c -15 /home/test.txt
```

### sort 排序文本文件
源文件不改变
```bash
$ sort test2
```
`-o`指定输出文件（也可以使用重定向）
```bash
$ sort -o mysortfile.txt test.txt
```

### join 联合一致的文件

合并的文件必须是`一致的`，首先需要进行排序`sort`操作。
```bash
DamiantekiMacBook-Pro:demo Damian$ cat english 
lisi 60
wangwu 78
zhangsan 90

DamiantekiMacBook-Pro:demo Damian$ cat math 
lisi 90
wangwu 67
zhangsan 88

DamiantekiMacBook-Pro:demo Damian$ join math english 
lisi 90 60
wangwu 67 78
zhangsan 88 90
```

### paste 合并文件内容

以`列`为单位，进行合并。
将file2添加到file1
```bash
$ paste file1 file2 > newfile
```




### split 分割文件

```bash
$ split hive.log
```
在当前文件夹下产生很多小文件。

### unexpand 将 空格 转换为 Tab

`-t`将指定个数的空格转换为一个tab
```bash
$ unexpand -t 7 test.txt
```

### tr 替换文本字符（非字符串）
以字符为单位，不是字符串。会针对所有对应的字符进行操作。

e.g. 
替换 dev 为 xyz 。 采用输入重定向
```bash
$ tr dev zyx  < /home/test.txt
```
将小写字母换成大写字母
```bash
$ tr a-z A-Z < /home/test.txt
```
`-d`删除字符(删除所有存在的字符)
```bash
$ tr -d wang < /home/test.txt
```
替换字符
```bash
$ echo $PATH | tr ":" "\n"
```

### tee 保存到多个文件
```bash
$ cat demo.sh | tee file1 file2 file 3 file4
```

### tac 反序行
相对于 cat，最后一行第一个输出
```bash
$ tac /home/demo.sh
```

### spell 拼写检查
```bash
$ spell /home/demo.txt
```
输出拼写错误的单词


### diff 定位不同之处
```bash
$ diff file1 file2
```
在生成补丁程序的时候，很有用。比较新老版本的不同。

### cmp 对比任意两个文件
```bash
$ cmp file1 file2
```
比较两个指令
```bash
$ cmp /bin/ls /bin/mail
```
cmp 和 diff 的区别：

 - cmp   不指名具体的出错地方；可以用于文本文件以外的文件对比
 - diff  指明出错的地方；

  






