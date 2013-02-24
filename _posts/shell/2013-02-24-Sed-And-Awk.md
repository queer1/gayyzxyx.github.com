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

### 1.在`shell`命令中调用`sed`：

	sed [选项] 'sed命令' 输入文件

### 2.将`sed`命令插入脚本文件，然后通过`sed`命令来调用：

	sed [选项] -f sed脚本文件 输入文件

### 3.将`sed`插入脚本文件，这是该脚本为可执行，以`#!`符号开头：

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

使用'-e`选项可以同时执行多个sed编辑命令，比如我们要同时打印出匹配行和匹配行所在的行号，就需要向`sed`传递`p`和`=`两个指令，如下：

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


