#shell 学习笔记

[TOC]
























































---

###shell基础知识

因为Shell似乎是各UNIX系统之间通用的功能，并且经过了`POSIX`的标准化。因此，Shell脚本只要“用心写”一次，即可应用到很多系统上。因此，之所以要使用Shell脚本是基于：
 - 简单性：Shell是一个高级语言；通过它，你可以简洁地表达复杂的操作。
 - 可移植性：使用POSIX所定义的功能，可以做到脚本无须修改就可在不同的系统上执行。
 - 开发容易：可以在短时间内完成一个功能强大又妤用的脚本。

Unix/Linux上常见的Shell脚本解释器有 `bash`、`sh`、`csh`、`ksh` 等，习惯上把它们称作一种Shell。我们常说有多少种Shell，其实说的是**Shell脚本解释器**。

####bash

bash是Linux标准默认的shell，本教程也基于bash讲解。bash由Brian Fox和Chet Ramey共同完成，是BourneAgain Shell的缩写，内部命令一共有40个。

Linux使用它作为默认的shell是因为它有诸如以下的特色：

 1. 可以使用类似DOS下面的doskey的功能，用方向键查阅和快速输入并修改命令。
 2. 自动通过查找匹配的方式给出以某字符串开头的命令。
 3. 包含了自身的帮助功能，你只要在提示符下面键入help就可以得到相关的帮助。

####sh

sh 由Steve Bourne开发，是Bourne Shell的缩写，**sh 是 Unix 标准默认的shell**。




####考虑到Shell脚本的命令限制和效率问题，下列情况一般不使用Shell
 1. **资源密集型的任务**，尤其在需要考虑效率时（比如，排序，hash等等）。
 2. 需要处理大任务的**数学操作**，尤其是浮点运算，精确运算，或者复杂的算术运算（这种情况一般使用C++或FORTRAN 来处理）
 3. 有跨平台（操作系统）移植需求（一般使用C 或Java）。
 4. 复杂的应用，在必须使用 **结构化编程** 的时候（需要变量的类型检查，函数原型，等等）。
 5. 对于影响系统全局性的关键任务应用。
 6. 对于 **安全** 有很高要求的任务，比如你需要一个健壮的系统来防止入侵、破解、恶意破坏等等。
 7. 项目由连串的依赖的各个部分组成。
 8. 需要大规模的**文件操作**。
 9. 需要**多维数组**的支持。
 10. 需要**数据结构**的支持，比如链表或数等数据结构。
 11. 需要产生或操作**图形化界面 GUI**。
 12. 需要直接操作系统硬件。
 13. 需要 I/O 或socket 接口。
 14. 需要使用库或者遗留下来的老代码的接口。
 15. **私人的、闭源的应用**（shell 脚本把代码就放在文本文件中，全世界都能看到）。

如果你的应用符合上边的任意一条，那么就考虑一下更强大的语言吧——或许是Perl、Tcl、Python、Ruby——或者是更高层次的编译语言比如C/C++，或者是Java。
即使如此，你会发现，使用shell来原型开发你的应用，在开发步骤中也是非常有用的。


###shell 初步

打开文本编辑器，新建一个文件，扩展名为`sh`（sh代表shell），**扩展名并不影响脚本执行，见名知意就好**，如果你用php写shell 脚本，扩展名就用php好了。

输入一些代码：
```bash
#!/bin/bash
echo "Hello World !"
```
“`#!`” 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种Shell。
`echo命令`用于向窗口输出文本。

####运行Shell脚本有三种方法
#####作为可执行程序

将上面的代码保存为test.sh，并 cd 到相应目录：
```bash
chmod +x ./test.sh  #使脚本具有执行权限
./test.sh  #执行脚本
```
注意，一定要写成`./test.sh`，而不是test.sh。运行其它二进制的程序也一样，直接写test.sh，linux系统会去PATH里寻找有没有叫test.sh的，而只有/bin, /sbin, /usr/bin，/usr/sbin等在PATH里，你的当前目录通常不在PATH里，所以写成test.sh是会找不到命令的，要用./test.sh告诉系统说，就在当前目录找。

>通过这种方式运行bash脚本，第一行一定要写对，好让系统查找到正确的解释器。

这里的"系统"，其实就是shell这个应用程序（想象一下Windows Explorer），但我故意写成系统，是方便理解，既然这个系统就是指shell，那么一个使用/bin/sh作为解释器的脚本是不是可以省去第一行呢？是的。

使用别的默认程序。下面使用`cat`作为默认的解释器：
```bash
#!/bin/cat
echo "Hello World !"
```
运行后，输出
```bash
#!/bin/cat
echo "Hello World !"
```
这个结果和直接使用 ‘cat test.sh’ 的结果一样。

#####作为解释器参数

这种运行方式是，直接运行解释器，其参数就是shell脚本的文件名，如：
```bash
/bin/sh test.sh
/bin/php test.php
```
这种方式运行的脚本，**不需要在第一行指定解释器信息**，写了也没用。

再看一个例子:
下面的脚本使用 `read 命令`从 `stdin` 获取输入并赋值给 PERSON 变量，最后在 `stdout` 上输出：
```bash
#!/bin/bash

# Author : mozhiyan
# Copyright (c) http://see.xidian.edu.cn/cpp/linux/
# Script follows here:

echo "What is your name?"
read PERSON
echo "Hello, $PERSON"
```
运行脚本,输出下面结果：
```bash
$ chmod +x ./test.sh
$ sh test.sh

What is your name?
mozhiyan
Hello, mozhiyan
$
```

#####source方式执行
source命令是一种Shell的内部命令，其功能是读取指定的Shell程序文件，并依次执行其中的语句。这个命令和前两种方式的区别在于，source只是简单的读取脚本里面的语句，并且依次在`当前的shell`中执行，并没有创建新的子shell进程。

>脚本里面所创建的`变量`都会保存到当前的shell里面。


###Shell变量
Shell支持自定义变量。
####定义变量
定义变量时，变量名不加美元符号（$），如：
```bash
variableName="value"
```
>注意: **变量名和等号之间不能有空格**，这可能和你熟悉的所有编程语言都不一样。

同时，`变量名命名`须遵循如下规则：

1. 首个字符必须为字母（a-z，A-Z）
2. 中间不能有空格，可以使用下划线（_）
3. 不能使用标点符号
4. 不能使用bash里的关键字（可用help命令查看保留关键字）

e.g.
```bash
myUrl="http://see.xidian.edu.cn/cpp/linux/"
myNum=100
```
####使用变量

使用一个定义过的变量，只要在变量名前面加美元符号（`$`）即可，如：
```bash
your_name="mozhiyan"
echo $your_name
echo ${your_name}
```
变量名外面的`花括号{}`是可选的，加不加都行，**加花括号是为了帮助解释器识别变量的边界**。
比如下面这种情况：
```bash
for skill in Ada Coffe Action Java 
do
    echo "I am good at ${skill}Script"
done
```
如果不给skill变量加花括号，写成echo "I am good at $skillScript"，解释器就会把$skillScript当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。

>推荐给所有变量加上花括号，这是个好的编程习惯。

####重新定义变量

已定义的变量，可以被重新定义，如：
```bash
myUrl="http://see.xidian.edu.cn/cpp/linux/"
echo ${myUrl}

myUrl="http://see.xidian.edu.cn/cpp/shell/"
echo ${myUrl}
```

这样写是合法的，但注意，第二次赋值的时候不能写 
```bash
#错误用法
$myUrl="http://see.xidian.edu.cn/cpp/shell/"
```
**使用变量的时候才加美元符（$）**。

####只读变量

使用 `readonly 命令`可以将变量定义为只读变量，只读变量的值不能被改变。

下面的例子尝试更改只读变量，结果报错：
```bash
#!/bin/bash

$ myUrl="http://see.xidian.edu.cn/cpp/shell/"
$ readonly myUrl
$ myUrl="http://see.xidian.edu.cn/cpp/danpianji/"
```
运行后有提示：
```bash
/bin/sh: NAME: This variable is read only.
```
####删除变量

使用 unset 命令可以删除变量。语法：
```bash
unset variable_name
```

注意：
 - 变量被删除后不能再次使用
 - unset 命令不能删除只读变量（readonly ）

举个例子：
```bash
#!/bin/sh

myUrl="http://see.xidian.edu.cn/cpp/u/xitong/"
unset myUrl
echo $myUrl
```
上面的脚本没有任何输出。

####变量类型

运行shell时，会同时存在三种变量：

1) **局部变量**
局部变量在脚本或命令中定义，**仅在当前shell实例中有效**，其他shell启动的程序不能访问局部变量。

2) **环境变量**
所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。
必要的时候shell脚本也可以定义环境变量。

3) **shell变量**
shell变量是由shell程序设置的特殊变量。
shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

###Shell特殊变量
前面已经讲到，变量名只能包含数字、字母和下划线，因为某些包含其他字符的变量有特殊含义，这样的变量被称为`特殊变量`。

例如，`$` 表示当前Shell进程的ID，即 **pid**，看下面的代码：
```bash
$echo $$
```
运行结果
```
29949
```

特殊变量列表

|变量|含义|
|:-----|:---|
|$0	|当前脚本的文件名
|$n	|传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。
|$#	|传递给脚本或函数的`参数个数`。
|$*	|传递给脚本或函数的所有参数。
|$@	|传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同，下面将会讲到。
|$?	|上个命令的`退出状态`，或函数的返回值。
|$$	|当前Shell`进程ID`。对于 Shell 脚本，就是这些脚本所在的进程ID。

####命令行参数

运行脚本时传递给脚本的参数称为`命令行参数`。
命令行参数用 $n 表示，例如，$1 表示第一个参数，$2 表示第二个参数，依次类推。

请看下面的脚本：
```bash
#!/bin/bash

echo "File Name: $0"
echo "First Parameter : $1"
echo "First Parameter : $2"
echo "Quoted Values: $@"
echo "Quoted Values: $*"
echo "Total Number of Parameters : $#"
```
运行结果：
```bash
$./test.sh Zara Ali

File Name : ./test.sh
First Parameter : Zara
Second Parameter : Ali
Quoted Values: Zara Ali
Quoted Values: Zara Ali
Total Number of Parameters : 2
```


`$*` 和 `$@` 的区别:

`$*` 和 `$@` 都表示传递给函数或脚本的所有参数，**不被双引号(" ")包含时**，都以"$1" "$2" … "$n" 的形式输出所有参数。

但是**当它们被双引号(" ")包含时**，

 - "`$*`" 会将所有的参数作为一个整体，以"`$1 $2 … $n`"的形式输出所有参数；
 - "`$@`" 会将各个参数分开，以`"$1" "$2" … "$n"` 的形式输出所有参数。

下面的例子可以清楚的看到 `$*` 和 `$@` 的区别：
```bash
#!/bin/bash
echo "\$*=" $*
echo "\"\$*\"=" "$*"

echo "\$@=" $@
echo "\"\$@\"=" "$@"

echo "print each param from \$*"
for var in $*
do
    echo "$var"
done

echo "print each param from \$@"
for var in $@
do
    echo "$var"
done

#添加引号，整体输出
echo "print each param from \"\$*\""
for var in "$*"
do
    echo "$var"
done

#添加引号，分批输出
echo "print each param from \"\$@\""
for var in "$@"
do
    echo "$var"
done
```
执行 
```bash
./test.sh "a" "b" "c" "d"
```
看到下面的结果：
```bash
$*=  a b c d
"$*"= a b c d
$@=  a b c d
"$@"= a b c d
print each param from $*
a
b
c
d
print each param from $@
a
b
c
d
print each param from "$*"
a b c d
print each param from "$@"
a
b
c
d
```
####退出状态

`$?` 可以获取上一个命令的退出状态。
`退出状态`，就是上一个命令执行后的返回结果。

>退出状态是一个数字。
一般情况下，大部分命令执行**成功**会返回 0，**失败**返回 1。

>不过，也有一些命令返回其他值，表示不同类型的错误。

下面例子中，命令成功执行：
```bash
$./test.sh Zara Ali
File Name : ./test.sh
First Parameter : Zara
Second Parameter : Ali
Quoted Values: Zara Ali
Quoted Values: Zara Ali
Total Number of Parameters : 2
$echo $?
0
$
```
$? 也可以表示函数的返回值，后续将会讲解。

###Shell替换
如果表达式中包含特殊字符，Shell 将会进行替换。
例如，在双引号中使用变量就是一种替换，转义字符也是一种替换。

举个例子：
```bash
#!/bin/bash

a=10
echo -e "Value of a is $a \n"
```
运行结果：
```
Value of a is 10
```
这里 `-e` 表示**对转义字符进行替换**。如果不使用 -e 选项，将会原样输出：
```
Value of a is 10\n
```
下面的转义字符都可以用在 echo 中：

|转义字符	|含义|
|---|---|
|`\\`|	反斜杠
|`\a`|	警报，响铃
|`\b`|	退格（删除键）
|`\f`|	换页(FF)，将当前位置移到下页开头
|`\n`|	换行
|`\r`|	回车
|`\t`|	水平制表符（tab键） 
|`\v`|	垂直制表符

可以使用 **echo 命令**的 
`-E` 选项禁止转义，默认也是不转义的；
`-e` 表示**对转义字符进行替换**。
`-n` 选项可以禁止插入换行符。

####命令替换

命令替换是指Shell可以先执行命令，将输出结果`暂时保存`，在适当的地方输出。

命令替换的语法：
>注意是反引号，不是单引号，这个键位于 Esc 键下方。
```bash
`command`
```



下面的例子中，**将命令执行结果保存在变量**中：
```bash
#!/bin/bash

DATE=`date`
echo "Date is $DATE"

USERS=`who | wc -l`
echo "Logged in user are $USERS"

UP=`date ; uptime`
echo "Uptime is $UP"
```
运行结果：
```bash
Date is Thu Jul  2 03:59:57 MST 2009
Logged in user are 1
Uptime is Thu Jul  2 03:59:57 MST 2009
03:59:57 up 20 days, 14:03,  1 user,  load avg: 0.13, 0.07, 0.15
```

####变量替换

变量替换可以根据**变量的状态**（是否为空、是否定义等）来改变它的值

可以使用的变量替换形式：

|形式 | 说明 |
|----|----|
|${var}| 变量本来的值
|${var`:-word`} |   `-word`如果变量 var 为空或已被删除(unset)，那么返回 word，但不改变 var 的值。
|${var`:=word`}  |  `=word`如果变量 var 为空或已被删除(unset)，那么返回 word，并将 var 的值设置为 word。
|${var`:?message`}| `?word`如果变量 var 为空或已被删除(unset)，那么将消息 message 送到标准错误输出，可以用来检测变量 var 是否可以被正常赋值。**若此替换出现在Shell脚本中，那么脚本将停止运行。**
|${var`:+word`}   | `+word`如果变量 var 被定义，那么返回 word，但不改变 var 的值。

请看下面的例子：
```bash
#!/bin/bash

echo ${var:-"Variable is not set"}
echo "1 - Value of var is ${var}"

echo ${var:="Variable is not set"}
echo "2 - Value of var is ${var}"

unset var
echo ${var:+"This is default value"}
echo "3 - Value of var is $var"

var="Prefix"
echo ${var:+"This is default value"}
echo "4 - Value of var is $var"

echo ${var:?"Print this message"}
echo "5 - Value of var is ${var}"
```
运行结果：
```bash
Variable is not set
1 - Value of var is
Variable is not set
2 - Value of var is Variable is not set

3 - Value of var is
This is default value
4 - Value of var is Prefix
Prefix
5 - Value of var is Prefix
```

###Shell运算符
Bash 支持很多运算符，包括算数运算符、关系运算符、布尔运算符、字符串运算符和文件测试运算符。

**原生bash不支持简单的数学运算**，但是可以通过其他命令来实现，例如 `awk` 和 `expr`，`expr` 最常用。

`expr` 是一款表达式计算工具，使用它能完成表达式的求值操作。

例如，两个数相加：
```bash
#!/bin/bash

val=`expr 2 + 2`
echo "Total value : $val"
```
运行脚本输出：
```
Total value : 4
```
两点注意：

 1. **表达式和运算符之间要有空格**，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
 2. 完整的表达式要被 反引号`` 包含，注意这个字符不是常用的单引号，在 Esc 键下边。

####算术运算符

先来看一个使用算术运算符的例子：
>乘号(*)前边必须加反斜杠`\`才能实现乘法运算

```bash
#!/bin/sh

a=10
b=20
val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a is equal to b"
fi

if [ $a != $b ]
then
   echo "a is not equal to b"
fi
```
运行结果：

```bash
a + b : 30
a - b : -10
a * b : 200
b / a : 2
b % a : 0
a is not equal to b
```

>注意：条件表达式要放在方括号之间，并且要有空格。
例如 [$a==$b] 是错误的，必须写成 [ $a == $b ]。

####关系运算符

关系运算符只支持数字，不支持字符串，除非字符串的值是数字。

先来看一个关系运算符的例子：
```bash
#!/bin/sh

a=10
b=20
if [ $a -eq $b ]
then
   echo "$a -eq $b : a is equal to b"
else
   echo "$a -eq $b: a is not equal to b"
fi

if [ $a -ne $b ]
then
   echo "$a -ne $b: a is not equal to b"
else
   echo "$a -ne $b : a is equal to b"
fi

if [ $a -gt $b ]
then
   echo "$a -gt $b: a is greater than b"
else
   echo "$a -gt $b: a is not greater than b"
fi

if [ $a -lt $b ]
then
   echo "$a -lt $b: a is less than b"
else
   echo "$a -lt $b: a is not less than b"
fi

if [ $a -ge $b ]
then
   echo "$a -ge $b: a is greater or  equal to b"
else
   echo "$a -ge $b: a is not greater or equal to b"
fi

if [ $a -le $b ]
then
   echo "$a -le $b: a is less or  equal to b"
else
   echo "$a -le $b: a is not less or equal to b"
fi
```
运行结果：
```
10 -eq 20: a is not equal to b
10 -ne 20: a is not equal to b
10 -gt 20: a is not greater than b
10 -lt 20: a is less than b
10 -ge 20: a is not greater or equal to b
10 -le 20: a is less or  equal to b
```

####布尔运算符

先来看一个布尔运算符的例子：
```bash
#!/bin/sh

a=10
b=20

if [ $a != $b ]
then
   echo "$a != $b : a is not equal to b"
else
   echo "$a != $b: a is equal to b"
fi

if [ $a -lt 100 -a $b -gt 15 ]
then
   echo "$a -lt 100 -a $b -gt 15 : returns true"
else
   echo "$a -lt 100 -a $b -gt 15 : returns false"
fi

if [ $a -lt 100 -o $b -gt 100 ]
then
   echo "$a -lt 100 -o $b -gt 100 : returns true"
else
   echo "$a -lt 100 -o $b -gt 100 : returns false"
fi

if [ $a -lt 5 -o $b -gt 100 ]
then
   echo "$a -lt 100 -o $b -gt 100 : returns true"
else
   echo "$a -lt 100 -o $b -gt 100 : returns false"
fi
```
运行结果：
```bash
10 != 20 : a is not equal to b
10 -lt 100 -a 20 -gt 15 : returns true
10 -lt 100 -o 20 -gt 100 : returns true
10 -lt 5 -o 20 -gt 100 : returns false
```
布尔运算符列表

|运算符 |说明 | 举例|
|----|----|----|
|`!`   |非运算，表达式为 true 则返回 false，否则返回 true。 | [ ! false ] 返回 true。
|`-o`  |或运算，有一个表达式为 true 则返回 true。 | [ $a -lt 20 -o $b -gt 100 ] 返回 true。
|`-a`  |与运算，两个表达式都为 true 才返回 true。 | [ $a -lt 20 -a $b -gt 100 ] 返回 false。


####文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性。

例如，变量 file 表示文件“/var/www/tutorialspoint/unix/test.sh”，它的大小为100字节，具有 rwx 权限。下面的代码，将检测该文件的各种属性：
```bash
#!/bin/sh

file="/var/www/tutorialspoint/unix/test.sh"

if [ -r $file ]
then
   echo "File has read access"
else
   echo "File does not have read access"
fi

if [ -w $file ]
then
   echo "File has write permission"
else
   echo "File does not have write permission"
fi

if [ -x $file ]
then
   echo "File has execute permission"
else
   echo "File does not have execute permission"
fi

if [ -f $file ]
then
   echo "File is an ordinary file"
else
   echo "This is sepcial file"
fi

if [ -d $file ]
then
   echo "File is a directory"
else
   echo "This is not a directory"
fi

if [ -s $file ]
then
   echo "File size is zero"
else
   echo "File size is not zero"
fi

if [ -e $file ]
then
   echo "File exists"
else
   echo "File does not exist"
fi
```
运行结果：
```bash
File has read access
File has write permission
File has execute permission
File is an ordinary file
This is not a directory
File size is zero
File exists
```

文件测试运算符列表

|操作符&说明|  举例|
|---|---|
|`-b` file 检测文件是否是块设备文件，如果是，则返回 true。  |[ -b $file ] 返回 false。
|`-c` file 检测文件是否是字符设备文件，如果是，则返回 true。 |[ -b $file ] 返回 false。
|`-d` file 检测文件是否是目录，如果是，则返回 true。 |[ -d $file ] 返回 false。
|`-f` file 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。    |[ -f $file ] 返回 true。
|`-g` file 检测文件是否设置了 SGID 位，如果是，则返回 true。  |[ -g $file ] 返回 false。
|`-k` file 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  |[ -k $file ] 返回 false。
|`-p` file 检测文件是否是具名管道，如果是，则返回 true。   |[ -p $file ] 返回 false。
|`-u` file 检测文件是否设置了 SUID 位，如果是，则返回 true。  |[ -u $file ] 返回 false。
|`-r` file 检测文件是否可读，如果是，则返回 true。  |[ -r $file ] 返回 true。
|`-w` file 检测文件是否可写，如果是，则返回 true。  |[ -w $file ] 返回 true。
|`-x` file 检测文件是否可执行，如果是，则返回 true。 |[ -x $file ] 返回 true。
|`-s` file 检测文件是否为空（文件大小是否大于0），不为空返回 true。 |[ -s $file ] 返回 true。
|`-e` file 检测文件（包括目录）是否存在，如果是，则返回 true。    |[ -e $file ] 返回 true。



###Shell字符串
字符串是shell编程中最常用最有用的数据类型（除了数字和字符串，也没啥其它类型好用了），字符串可以用**单引号**，也可以用**双引号**，也可以不用引号。
单双引号的区别跟PHP类似。

####单引号
```bash
str='this is a string'
```
单引号字符串的限制：

 1. 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
 2. 单引号字串中不能出现单引号（对单引号**使用转义符后也不行**）。

####双引号
```bash
your_name='qinjx'
str="Hello, I know your are \"$your_name\"! \n"
```
双引号的优点：

 1. 双引号里**可以有变量**
 2. 双引号里**可以出现转义字符**

####拼接字符串
`str="str1"${str2}"!"`
```bash
your_name="qinjx"
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"

echo $greeting $greeting_1
```
####获取字符串长度
`${#str}`
```bash
string="abcd"
echo ${#string} #输出 4
```
####提取子字符串
`${str:startPos:endPos}`
```bash
string="alibaba is a great company"
echo ${string:1:4} #输出liba
```
####查找子字符串
`expr index "${str}" subStr`
```bash
string="alibaba is a great company"
echo `expr index "$string" is`
```

###shell数组
Shell在编程方面比Windows批处理强大很多，无论是在循环、运算。

**bash支持一维数组（不支持多维数组），并且没有限定数组的大小。**
类似与C语言，数组元素的`下标由0开始`编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于0。
####定义数组

在Shell中，用`括号`来表示数组，数组元素用“`空格`”符号分割开。

定义数组的一般形式为：
```bash
array_name=(value1 ... valuen)
```
例如：
```bash
array_name=(value0 value1 value2 value3)
或者
array_name=(
value0
value1
value2
value3
)
```
还可以单独定义数组的各个分量：
```bash
array_name[0]=value0
array_name[1]=value1
array_name[2]=value2
```
可以不使用连续的下标，而且下标的范围没有限制。

####读取数组

读取数组元素值的一般格式是：
```bash
${array_name[index]}
```
举个例子：
```bash
#!/bin/sh
NAME[0]="Zara"
NAME[1]="Qadir"
NAME[2]="Mahnaz"
NAME[3]="Ayan"
NAME[4]="Daisy"
echo "First Index: ${NAME[0]}"
echo "Second Index: ${NAME[1]}"
```
运行脚本，输出：
```bash
$./test.sh
First Index: Zara
Second Index: Qadir
```
使用`@` 或` *` 可以获取数组中的所有元素，例如：
```bash
${array_name[*]}
${array_name[@]}
```
举个例子：
```bash
#!/bin/sh
NAME[0]="Zara"
NAME[1]="Qadir"
NAME[2]="Mahnaz"
NAME[3]="Ayan"
NAME[4]="Daisy"
echo "First Method: ${NAME[*]}"
echo "Second Method: ${NAME[@]}"
```
运行脚本，输出：
```bash
$./test.sh
First Method: Zara Qadir Mahnaz Ayan Daisy
Second Method: Zara Qadir Mahnaz Ayan Daisy
```
####获取数组的长度

获取数组长度的方法与获取字符串长度的方法相同，例如：
```bash
# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}

# 取得数组单个元素的长度
lengthn=${#array_name[n]}
```

##shell 命令学习

###Shell echo命令
echo是Shell的一个内部指令，用于在屏幕上打印出指定的字符串。命令格式：
```bash
echo arg
```
您可以使用echo实现更复杂的输出格式控制。

**显示转义字符**
```bash
echo "\"It is a test\""
```
结果将是：
```
"It is a test"
```
双引号也可以省略。

**显示变量**

```bash
name="OK"
echo "$name It is a test"
```
结果将是：
```
OK It is a test
```
同样双引号也可以省略。

如果变量与其它字符相连的话，需要使用大括号（`{ }`）：
```bash
mouth=8
echo "${mouth}-1-2009"
```
结果将是：
```
8-1-2009
```

**显示换行**
```bash
echo "OK!\n"
echo "It is a test"
```
输出：
```
OK!
It is a test
```

**显示不换行**
```bash
echo "OK!\c"
echo "It is a test"
```
输出：
```
OK!It si a test
```

**显示结果重定向至文件**
```bash
echo "It is a test" > myfile
```

**原样输出字符串**

若需要原样输出字符串（不进行转义），请使用`单引号`。例如：
```bash
echo '$name\"'
```

**显示命令执行结果**
反引号
```bash
echo `date`
```
结果将显示当前日期

>从上面可看出，双引号可有可无，`单引号`主要用在原样输出中。

###Shell if else语句

if 语句通过关系运算符判断表达式的真假来决定执行哪个分支。
Shell 有三种 if ... else 语句：

 - if ... fi 语句；
 - if ... else ... fi 语句；
 - if ... elif ... else ... fi 语句。

1) if ... else 语句

if ... else 语句的语法：
```bash
if [ expression ]
then
   Statement(s) to be executed if expression is true
fi
```
如果 expression 返回 true，then 后边的语句将会被执行；如果返回 false，不会执行任何语句。

最后必须以 fi 来结尾闭合 if，fi 就是 if 倒过来拼写，后面也会遇见。

>注意：expression 和方括号([ ])之间必须有`空格`，否则会有语法错误。

举个例子：
```bash
#!/bin/sh
a=10
b=20
if [ $a == $b ]
then
   echo "a is equal to b"
fi
if [ $a != $b ]
then
   echo "a is not equal to b"
fi
```
运行结果：
```
a is not equal to b
```

2) if ... else ... fi 语句

if ... else ... fi 语句的语法：
```bash
if [ expression ]
then
   Statement(s) to be executed if expression is true
else
   Statement(s) to be executed if expression is not true
fi
```
如果 expression 返回 true，那么 then 后边的语句将会被执行；否则，执行 else 后边的语句。

举个例子：
```bash
#!/bin/sh
a=10
b=20
if [ $a == $b ]
then
   echo "a is equal to b"
else
   echo "a is not equal to b"
fi
```
执行结果：
```
a is not equal to b
```
3) if ... elif ... fi 语句

if ... elif ... fi 语句可以对多个条件进行判断，语法为：
```bash
if [ expression 1 ]
then
   Statement(s) to be executed if expression 1 is true
elif [ expression 2 ]
then
   Statement(s) to be executed if expression 2 is true
elif [ expression 3 ]
then
   Statement(s) to be executed if expression 3 is true
else
   Statement(s) to be executed if no expression is true
fi
```
哪一个 expression 的值为 true，就执行哪个 expression 后面的语句；如果都为 false，那么不执行任何语句。

举个例子：
```bash
#!/bin/sh
a=10
b=20
if [ $a == $b ]
then
   echo "a is equal to b"
elif [ $a -gt $b ]
then
   echo "a is greater than b"
elif [ $a -lt $b ]
then
   echo "a is less than b"
else
   echo "None of the condition met"
fi
```
运行结果：
```
a is less than b
```
if ... else 语句也可以写成一行，以命令的方式来运行，像这样：
```bash
if test $[2*3] -eq $[1+5]; then echo 'The two numbers are equal!'; fi;
```
if ... else 语句也经常与 `test 命令`结合使用，如下所示：
```bash
num1=$[2*3]
num2=$[1+5]
if test $[num1] -eq $[num2]
then
    echo 'The two numbers are equal!'
else
    echo 'The two numbers are not equal!'
fi
```
输出：
```bash
The two numbers are equal!
```
`test 命令`用于检查某个条件是否成立，与方括号([ ])类似。

###Shell case esac语句
`case ... esac` 与其他语言中的 switch ... case 语句类似，是一种多分枝选择结构。

case 语句匹配一个值或一个模式，如果匹配成功，执行相匹配的命令。
case语句格式如下：
```
case 值 in
模式1)
    command1
    command2
    command3
    ;;
模式2）
    command1
    command2
    command3
    ;;
*)
    command1
    command2
    command3
    ;;
esac
```
case工作方式如上所示。
取值后面必须为`关键字 in`，每一模式必须以`右括号`结束。**取值可以为变量或常数**。
匹配发现取值符合某一模式后，其间所有命令开始执行直至 `;;`。;; 与其他语言中的 break 类似，意思是跳到整个 case 语句的最后。

**取值将检测匹配的每一个模式**。
一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。
如果无一匹配模式，使用`星号 * `捕获该值，再执行后面的命令。

下面的脚本提示输入1到4，与每一种模式进行匹配：
```bash
echo 'Input a number between 1 to 4'
echo 'Your number is:\c'
read aNum
case $aNum in
    1)  echo 'You select 1'
    ;;
    2)  echo 'You select 2'
    ;;
    3)  echo 'You select 3'
    ;;
    4)  echo 'You select 4'
    ;;
    *)  echo 'You do not select a number between 1 to 4'
    ;;
esac
```
输入不同的内容，会有不同的结果，例如：
```
Input a number between 1 to 4
Your number is:3
You select 3
```
再举一个例子：
```bash
#!/bin/bash
option="${1}"
case ${option} in
   -f) FILE="${2}"
      echo "File name is $FILE"
      ;;
   -d) DIR="${2}"
      echo "Dir name is $DIR"
      ;;
   *) 
      echo "`basename ${0}`:usage: [-f file] | [-d directory]"
      exit 1 # Command to come out of the program with status 1
      ;;
esac
```

运行结果：
```bash
$./test.sh
test.sh: usage: [ -f filename ] | [ -d directory ]
$ ./test.sh -f index.htm
$ vi test.sh
$ ./test.sh -f index.htm
File name is index.htm
$ ./test.sh -d unix
Dir name is unix
$
```
###Shell for循环

与其他编程语言类似，Shell支持for循环。

for循环一般格式为：
```bash
for 变量 in 列表
do
    command1
    command2
    ...
    commandN
done
```
列表是一组值（数字、字符串等）组成的序列，每个值通过`空格`分隔。
每循环一次，就将列表中的下一个值赋给变量。

`in 列表`是可选的，如果不用它，for 循环使用命令行的位置参数。

例如，顺序输出当前列表中的数字：
```bash
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```
运行结果：
```
The value is: 1
The value is: 2
The value is: 3
The value is: 4
The value is: 5
```
顺序输出字符串中的字符：
```bash
for str in 'This is a string'
do
    echo $str
done
```
运行结果：
```
This is a string
```
显示主目录下以 .bash 开头的文件(使用正则表达式)：
```
#!/bin/bash
for FILE in $HOME/.bash*
do
   echo $FILE
done
```
运行结果：
```
/root/.bash_history
/root/.bash_logout
/root/.bash_profile
/root/.bashrc
```

###Shell while循环

while循环用于不断执行一系列命令，也用于从输入文件中读取数据；命令通常为测试条件。
其格式为：
```bash
while command
do
   Statement(s) to be executed if command is true
done
```
命令执行完毕，控制返回循环顶部，从头开始直至测试条件为假。

以下是一个基本的while循环，测试条件是：如果COUNTER小于5，那么返回 true。COUNTER从0开始，每次循环处理时，COUNTER加1。运行上述脚本，返回数字1到5，然后终止。
```bash
COUNTER=0
while [ $COUNTER -lt 5 ]
do
    COUNTER='expr $COUNTER+1'
    echo $COUNTER
done
```
运行脚本，输出：
```
1
2
3
4
5
```
**while循环可用于读取键盘信息**。
下面的例子中，输入信息被设置为变量FILM，按<Ctrl-D>结束循环。
```bash
echo 'type <CTRL-D> to terminate'
echo -n 'enter your most liked film: '
while read FILM
do
    echo "Yeah! great film the $FILM"
done
```

运行脚本，输出类似下面：

```
type <CTRL-D> to terminate
enter your most liked film: Sound of Music
Yeah! great film the Sound of Music
```

###Shell until循环
until 循环执行一系列命令直至条件为 true 时停止。
until 循环与 while 循环在处理方式上刚好相反。
一般while循环优于until循环，但在某些时候，也只是极少数情况下，until 循环更加有用。

until 循环格式为：
```bash
until command
do
   Statement(s) to be executed until command is true
done
```
command 一般为条件表达式，如果返回值为 false，则继续执行循环体内的语句，否则跳出循环。

例如，使用 until 命令输出 0 ~ 9 的数字：
```bash
```
#!/bin/bash
a=0
until [ ! $a -lt 10 ]
do
   echo $a
   a=`expr $a + 1`
done
```
运行结果：
```
0
1
2
3
4
5
6
7
8
9
```

###Shell输入输出重定向
Unix 命令默认从标准输入设备(stdin)获取输入，将结果输出到标准输出设备(stdout)显示。一般情况下，标准输入设备就是键盘，标准输出设备就是终端，即显示器。
####输出重定向

命令的输出不仅可以是显示器，还可以很容易的转移向到文件，这被称为`输出重定向`。

命令输出重定向的语法为：
```bash
$ command > file
```
输出重定向会覆盖文件内容.
如果不希望文件内容被覆盖，可以使用 `>>` 追加到文件末尾.



---
###参考链接
http://see.xidian.edu.cn/cpp/shell/
