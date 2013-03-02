---
layout: post
title: 文本处理神器sed与awk命令使用
category: shell
tags: [shell, sed, awk]
keywords: shell, sed, awk
---

## 概述：
在linux/unix系统有存在两种功能异常强大的文本处理工具：`sed`和`awk`,两个命令有很多的共同处，都能使用正则表达式进行模式匹配，但是`awk`更为强大，因为这是一种编程语言，可以对结构化数据进行操作，产生格式化的报表。

## sed命令基本用法

`sed`是一种非交互式的文本编辑器，它可以对文本文件和来自键盘的输入、文件重定向、字符串、变量甚至来自管道的文本进行编辑。他先从文本行或者标准输入中读取数据复制到缓冲区，然后取命令行的一个或者多个命令进行编辑，相比于vim的优势在于可以一次性处理所有的编辑任务，很快很高效。由于它只是在缓冲区对文件的副本编辑，因此不会影响到原始文件，除非将输出重定向到另一个文件:

	sed 'sed Command' input-file > result-file

对于文件重定向不止这一个方法，还可以通过`sed`编辑命令中的`w`选项将结果保存到某个文件中，后续介绍。
调用`sed`的三种方式：

1.在`shell`命令中调用`sed`：

	sed [选项] 'sed命令' 输入文件

2.将`sed`命令插入脚本文件，然后通过`sed`命令来调用：

	sed [选项] -f sed脚本文件 输入文件

3.将`sed`插入脚本文件，这是该脚本为可执行，以`#!`符号开头：

	./sed 脚本文件 输入文件

### sed命令选项及其意义：

<table class="table table-bordered table-striped">
    <tr><td>选项</td><td>意义</td></tr>
    <tr><td>-n</td><td>不打印所有行到标准输出</td></tr>
    <tr><td>-e</td><td>表示将下一个字符串解析为sed编辑命令，如果只传递一个编辑命令给sed，-e选项可以省略</td></tr>
    <tr><td>-f</td><td>表示正在调用sed脚本文件</td></tr>
</table>

使用`sed`先定位文本行然后再执行编辑命令，`sed`使用以下方式定位文本行，正则表达式表示很好用。

### sed命令定位文本的方法

<table class="table table-bordered table-striped">
    <tr><td>选项</td><td>意义</td></tr>
    <tr><td>x</td><td>x指定行号</td></tr>
    <tr><td>x,y</td><td>从x到y行之间的范围</td></tr>
    <tr><td>/pattern/</td><td>查询包含pattern匹配的行</td></tr>
    <tr><td>/pattern/pattern/</td><td>查询两个表达式之间的行</td></tr>
    <tr><td>/pattern/,x</td><td>从pattern匹配行开始到x号行结束</td></tr>
    <tr><td>x,/pattern/</td><td>从x行号开始匹配到pattern匹配行结束</td></tr>
    <tr><td>x,y!</td><td>查询不包括x和y行号的行</td></tr>
</table>

`sed`编辑命令对文本进行打印、删除、追加、插入、替换等操作，其提供了极为丰富的编辑命令，下面是一些常用的编辑命令

<table class="table table-bordered table-striped">
    <tr><td>选项</td><td>意义</td></tr>
    <tr><td>p</td><td>打印匹配行</td></tr>
    <tr><td>=</td><td>打印文件行号</td></tr>
    <tr><td>a\</td><td>在定位行号之后追加文本信息</td></tr>
    <tr><td>i\</td><td>在定位行号之前插入文本信息</td></tr>
    <tr><td>d</td><td>删除定位行</td></tr>
    <tr><td>c\</td><td>用新文本替换定位文本</td></tr>
    <tr><td>s</td><td>使用替换模式替换相应模式</td></tr>
    <tr><td>r</td><td>从另一个文件中读文本</td></tr>
    <tr><td>w</td><td>将文本写到一个文件，功能等同重定向 > </td></tr>
    <tr><td>y</td><td>变换字符</td></tr>
    <tr><td>q</td><td>第一个模式匹配完成后退出</td></tr>
    <tr><td>l</td><td>显示与八进制ASCII码等价的控制字符</td></tr>
    <tr><td>{}</td><td>在定位行执行命令组，功能等同命令选项的-e</td></tr>
    <tr><td>n</td><td>读取下一个输入行，用下一个命令处理新的行</td></tr>
    <tr><td>h</td><td>将模式缓冲区的文本复制到保持缓冲区</td></tr>
    <tr><td>H</td><td>将模式缓冲区的文本追加到保持缓冲区</td></tr>
    <tr><td>x</td><td>互换模式缓冲区和保持缓冲区的内容</td></tr>
    <tr><td>g</td><td>将保持缓冲区的内容复制到模式缓冲区中</td></tr>
    <tr><td>G</td><td>将保持缓冲区的内容追加到模式缓冲区中</td></tr>
</table>

### 详解各选项

本次用到的测试文本如下：

	This is a Certificate Request file:
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:
	
	/O=Grid/OU=GlobusTest/OU=simpleCA-seugrid1.seu.edu.cn/OU=seu.edu.cn/CN=globus
	
	Te above string is known as your user certificate subject, and it uniquely identifies this user. $88
	To install this user certificate, please save this e-mail message into the following file.


#### 1.sed的-n选项

使用`-n`选项和不使用的区别是：使用后只输出匹配行，不使用的话输出所有行：

	#使用了-n选项，只打印第一行
	root@core /home/sed# sed -n '1p' input
	This is a Certificate Request file:
	#没使用-n选项，不仅打印第一行，还打印所有行
	root@core /home/sed# sed '1p' input
	This is a Certificate Request file:
	This is a Certificate Request file:
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:
	
	/O=Grid/OU=GlobusTest/OU=simpleCA-seugrid1.seu.edu.cn/OU=seu.edu.cn/CN=globus
	.......
	root@core /home/sed#

在参数p前面可以加上正则表达式控制打印范围，如下：

	#查找Certificate所在行，共有两处
	root@core /home/sed# sed -n '/Certificate/p' input
	This is a Certificate Request file:
	Certificate Subject:
	#打印2到5行
	root@core /home/sed# sed -n '2,5p' input
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:

### 2.sed的-e选项

使用`-e`选项可以同时执行多个sed编辑命令，比如我们要同时打印出匹配行和匹配行所在的行号，就需要向`sed`传递`p`和`=`两个指令，如下：

	#打印匹配行号
	root@core /home/sed# sed -n '/Certificate/=' input
	1
	5
	#打印匹配行号并且将匹配行打印出来
	root@core /home/sed# sed -n -e '/Certificate/=' -e '/Certificate/p' input
	1
	This is a Certificate Request file:
	5
	Certificate Subject:
	#这条命令和上面等价，只是将命令组用{}括起来
	root@core /home/sed# sed -n '/Certificate/{=;p}' input
	1
	This is a Certificate Request file:
	5
	Certificate Subject:

### 3.sed的基本编辑命令的i\命令

插入文本的基本格式为：
	
	sed 'specified-address i\text' input-file

新建名字为insert.sed的脚本文件如下：

	root@core /home/sed# cat insert.sed
	#!/bin/sed -f
	/file:/i\
	we insert a new line

接着执行insert.sed脚本，并加上input作为文件名参数，于是在于`file:`的匹配上方加入了新文本：

	root@core /home/sed# ./insert.sed  input
	we insert a new line                         #被插入的新文本
	This is a Certificate Request file:
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:
	
	/O=Grid/OU=GlobusTest/OU=simpleCA-seugrid1.seu.edu.cn/OU=seu.edu.cn/CN=globus
	
	Te above string is known as your user certificate subject, and it uniquely identifies this user. $88
	To install this user certificate, please save this e-mail message into the following file.

### 4.sed的基本编辑命令c\命令

这个是将匹配的文本用新文本替换，修改文本的格式为：

	sed 'specified-address c\text' input-file

新建一个修改文本的脚本命名为`modify.sed`，内容如下：

	root@core /home/sed# cat > modify.sed
	#!/bin/sed -f
	/file:/c\
	we midify this line.

意思是将file:关键字出现的那一行替换为we modify this line.

	root@core /home/sed# chmod u+x modify.sed
	root@core /home/sed# ./modify.sed input
	we midify this line.                             #整个行被替换后的结果
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:
	
	/O=Grid/OU=GlobusTest/OU=simpleCA-seugrid1.seu.edu.cn/OU=seu.edu.cn/CN=globus
	
	Te above string is known as your user certificate subject, and it uniquely identifies this user. $88
	To install this user certificate, please save this e-mail message into the following file.

### 5.sed的基本编辑命令d命令

sed的删除命令是指定范围内的删除，格式如下：

	sed 'specified-address d' input

删除第一行

	root@core /home/sed# sed '1d' input
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:

删除最后一行

	root@core /home/sed# sed '$d' input
	......            #多于的被省略
	
	Te above string is known as your user certificate subject, and it uniquely identifies this user. $88

还可以根据正则表达式删除匹配行，如

	sed '4,$d' input                    #删除第四行到最后一行
	sed '/Certificate/d' input          #删除带有Certificate的所有行

### 6.sed的基本编辑命令s命令

sed替换文本操作将匹配的文本行利用新文本替换，其命令的为`s`，替换文本的格式如下：

	sed 's/replaced-string/new-string/[option]' input-file

`s`表示的是替换文本的操作，`sed`命令首先匹配被替换的字符串，匹配成功后用新字符串替换它，替换选项`option`对替换操作进一步细化，如下：

<table class="table table-bordered table-striped">
    <tr><td>选项</td><td>意义</td></tr>
    <tr><td>g</td><td>表示替换文本中所有出现被替换字符串之处，即替换全部</td></tr>
    <tr><td>p</td><td>与-n选项结合，只打印替换行</td></tr>
    <tr><td>w file-name</td><td>表示将输出定向到另一个文件</td></tr>
</table>

默认情况下，sed s命令后将替换后的文本全部输出，如果要求只打印替换行，需要结合`-n`和`p`选项，如下：

	sed -n 's/replaced-string/new-string/p' input-file

需要注意的是如果缺少`p`选项将不会打印任何内容，这和前面说的`-n`打印所有内容有点出入

	root@core /home/sed# sed -n 's/Certificate/CERTIFICATE/p' input    
	This is a CERTIFICATE Request file:
	CERTIFICATE Subject:
	root@core /home/sed# sed -n 's/Certificate/CERTIFICATE/' input   #不带p，不打印任何内容
	root@core /home/sed#

从上面可以看到只要是出现了Certificate的都被替换成了CERTIFICATE，这样看是否'g'选项就没用了呢？不是的，`g`选项的作用是使得每一行中每次出现的关键字都被替换，如果不带`g`选项则表示替换当前行第一次出现的关键字，然后再跳到下一个匹配行，通过一个例子来说说明：

	root@core /home/sed# sed  -n 's/seu/gayyzxyx/p' input  #不带g，发现下面一行的只有一个seu被替换成了gayyzxyx
	/O=Grid/OU=GlobusTest/OU=simpleCA-gayyzxyxgrid1.seu.edu.cn/OU=seu.edu.cn/CN=globus
	root@core /home/sed# sed  -n 's/seu/gayyzxyx/pg' input   #带上g，一行中的所有ser被替换成了gayyzxyx
	/O=Grid/OU=GlobusTest/OU=simpleCA-gayyzxyxgrid1.gayyzxyx.edu.cn/OU=gayyzxyx.edu.cn/CN=globus
	root@core /home/sed#

所以当一行文本中，**出现了一次以上的关键字，这时的`g`选项才会有作用**
sed的替换命令还可以指定替换第几次匹配的关键字，只需要在替换选项上加上相应的数字即可，数字范围需要在1~512之间：

	root@core /home/sed# sed  -n 's/seu/gayyzxyx/2p' input
	/O=Grid/OU=GlobusTest/OU=simpleCA-seugrid1.gayyzxyx.edu.cn/OU=seu.edu.cn/CN=globus

可以理解，当参数`p`前面不加数字时，之所以为只匹配一行中第一次出现的位置，是因为默认就是1，所以只匹配第一个出现的位置。

`w`选项好理解，表示将输出定向到指定文件，如果输出文件还没有建立，sed就自动建立输出文件。

	root@core /home/sed# sed  -n 's/seu/gayyzxyx/w output' input
	root@core /home/sed# cat output
	/O=Grid/OU=GlobusTest/OU=simpleCA-gayyzxyxgrid1.seu.edu.cn/OU=seu.edu.cn/CN=globus

在sed文本替换中经常会使用到`&`符号，表示保存被资环的字符串以供调用，下面是两条等价的命令：

	root@core /home/sed# sed -n 's/seu/XX&/pg' input    #都是将seu替换为XXseu
	/O=Grid/OU=GlobusTest/OU=simpleCA-XXseugrid1.XXseu.edu.cn/OU=XXseu.edu.cn/CN=globus
	root@core /home/sed# sed -n 's/seu/XXseu/pg' input
	/O=Grid/OU=GlobusTest/OU=simpleCA-XXseugrid1.XXseu.edu.cn/OU=XXseu.edu.cn/CN=globus

### 7.sed基本编辑命令w命令

由于sed命令指示对缓冲区的内容进行编辑，如果需要保存结果，需要将编辑后的文本重定向到另一个文件，使用写入符号`w`，基本格式为:

	sed  'specified-address w output-file' input-file

`w`的用法与`sed`替换文本中的`w`选项相似：

	root@core /home/sed# sed -n '1,$ w output' input  #将input的1到最后一行输出到output
	root@core /home/sed# cat output                   #查看output
	This is a Certificate Request file:
	It should be mailed to yao.rid@gmail.com
	
	......   #省略很多行

同时也可以通过正则表达式输出匹配行到新文件中，如：

	root@core /home/sed# sed -n '/Certificate/ w output' input
	root@core /home/sed# cat output
	This is a Certificate Request file:
	Certificate Subject:

也可以通过重定向完成：

	root@core /home/sed# sed -n '/Certificate/p' input > output

### 7.sed基本编辑命令r命令

sed命令可以将其他文件中的文本读入，并附加在指定地址之后，sed读入文件的符号位`r`，基本格式为：

	sed 'specified-address r file-name' input-file

将Certificate后面加上以下otherfile中的内容：

	root@core /home/sed# cat > otherfile                #创建otherfile
	This is the first line of the otherfile.
	This is the second line of the otherfile.
	root@core /home/sed# sed '/Certificate/r otherfile' input    #追加
	This is a Certificate Request file:
	This is the first line of the otherfile.       #追加行
	This is the second line of the otherfile.
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:
	This is the first line of the otherfile.           #追加行
	This is the second line of the otherfile.
	
	...........................                   #省略行

### 8.sed基本编辑命令的q命令

sed的`q`选项表示完成指定地址的匹配后立即退出，基本格式为

	sed 'specified-address q' input-file

比如打印前五行然后退出：

	sed '5 q' input

和'p'命令的区别是：`p`命令是打印出匹配的所有行，而`q`是打印匹配的第一行

	root@core /home/sed# sed -n '/Certificate/p' input
	This is a Certificate Request file:
	Certificate Subject:
	root@core /home/sed# sed '/Certificate/q' input
	This is a Certificate Request file:

### 9.sed基本编辑命令的y命令

sed的`y`，命令表示字符变换，类似python的`string.maketrans()`，变换与被变换字符等长，格式如下：

	sed 'y/old-string/new-string/' input-file

比如将input文件中的1变A，2变B，3变C，4变D：

	sed 'y/1234/ABCD' input

### 10.sed基本编辑命令的l命令

sed打印控制字符，如退格键、F1键、shift键等，如果在文件中包含这么键，用`sed l`命令便可以打印出，如：

	sed -n '1,$l' input

### 10.sed基本编辑命令的{}命令

用`{}`将多条命令括起来组成一个命令组，与`-e`选项类似，如下面三条命令等价：

	root@core /home/sed# sed -n -e '/Certificate/p' -e '/Certificate/=' input
	root@core /home/sed# sed -n '/Certificate/{p;=}' input
	root@core /home/sed# sed -n '/Certificate/p;/Certificate/=' input

### 10.sed基本编辑命令的n命令

sed编辑命令`n`的意义是读取下一个输入行，用`n`后面的一个命令处理该行，由于此时一般都有多个编辑命令，所以通常需要与`{}`符号结合使用。下面一个例子是找出certificate关键字的匹配行，然后在匹配行的下一行执行将ll字符串替换成99.

	root@core /home/sed# sed '/certificate/{n;s/ll/99/}' input
	This is a Certificate Request file:
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:
	
	/O=Grid/OU=GlobusTest/OU=simpleCA-seugrid1.seu.edu.cn/OU=seu.edu.cn/CN=globus
	
	Te above string is known as your user certificate subject, and it uniquely identifies this user. $88
	To insta99 this user certificate, please save this e-mail message into the following file.  #原来是install,现在是insta99

### 11.sed基本编辑命令的h,x,g命令

sed有两种缓冲区，一种是模式缓冲区(Pattern Buffer)另一种是保持缓冲区(Hold Buffer),保持缓冲区是另一块内存空间，sed的一些命令可以对保持缓冲区进行修改并与模式缓冲区的内容互换。

	root@core /home/sed# sed -e '/Subject/h' -e '/seugrid/x' -e '$G' input
	This is a Certificate Request file:
	It should be mailed to yao.rid@gmail.com
	
	========================================
	Certificate Subject:    #匹配Subject，将此行写入保持缓冲区
	
	Certificate Subject:   #匹配seugrid，将此行写入保持缓冲区，并将原来保持缓冲区的内容输出
	
	Te above string is known as your user certificate subject, and it uniquely identifies this user. $88
	To install this user certificate, please save this e-mail message into the following file.
	/O=Grid/OU=GlobusTest/OU=simpleCA-seugrid1.seu.edu.cn/OU=seu.edu.cn/CN=globus
	#到最后一行时，输出保持缓冲区的内容	

太多内容了，`awk`还是坐电梯吧，[传送门](http://gayyzxyx.github.com/shell/2013/03/02/Awk/)




