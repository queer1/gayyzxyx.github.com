---
layout: post
title: 关于文件基本处理
category: shell
tags: [shell]
keywords: shell
---
文件处理围绕着sort、uniq、join、cut、paste、split、tr、tar等命令，这些与sed和awk组合起来构成了linux文本处理的所有命令和工具。

## 1\.sort命令

sort命令将输入文件看成是有多条记录组成的数据流，记录由可变宽度的字符组成，以换行符作为界定符。其与awk命令一样，也可以指令域分隔符，不一样的是sort的分割符是-t选项，而awk是F。sort的常用选项如下：

<table class="table table-bordered table-striped">
    <tr><td>选项</td><td>意义</td></tr>
    <tr><td>-c</td><td>测试文件是否已经被排序</td></tr>
    <tr><td>-k</td><td>指定排序的域</td></tr>
    <tr><td>-m</td><td>合并两个已排序的文件</td></tr>
    <tr><td>-n</td><td>根据数字大小进行排序</td></tr>
    <tr><td>-o[输出文件</td><td>重定向</td></tr>
    <tr><td>-r</td><td>将排序结果逆向显示</td></tr>
    <tr><td>-t</td><td>改变域分隔符</td></tr>
    <tr><td>-u</td><td>去除结果中的重复行</td></tr>
</table>

### 1.1\.-t选项

sort的默认分隔符是空格，在t后面紧跟分割符号就可以指定域

<pre>
root@raspberrypi:/home/pi/exercise# cat computer.db
thinkpad:USA:14000:2009:x301
thinkpad:HongKong:10000:2008:T400
thinkpad:USA:8000:2007:x60
HP:China:5600:2010:DM3
HP:China:12000:2010:NE808
SumSung:Korea:5400:2009:Q308
IdeaPad:China:8000:2007:U450
Acer:Taiwan:8000:2000:PT210

root@raspberrypi:/home/pi/exercise# cat computer.db|sort -t:  #指定分隔符:后
Acer:Taiwan:8000:2000:PT210
HP:China:12000:2010:NE808
HP:China:5600:2010:DM3
IdeaPad:China:8000:2007:U450
SumSung:Korea:5400:2009:Q308
thinkpad:HongKong:10000:2008:T400
thinkpad:USA:14000:2009:x301
thinkpad:USA:8000:2007:x60
</pre>

### 1.2\.-k选项

sort默认是按第一个域进行排序的，可以指定第n个域进行排序如下：

<pre>
root@raspberrypi:/home/pi/exercise# cat computer.db|sort -t: -k2
HP:China:12000:2010:NE808
HP:China:5600:2010:DM3
IdeaPad:China:8000:2007:U450
thinkpad:HongKong:10000:2008:T400
SumSung:Korea:5400:2009:Q308
Acer:Taiwan:8000:2000:PT210
thinkpad:USA:14000:2009:x301
thinkpad:USA:8000:2007:x60
</pre>

### 1.3\.-n选项

-n表示按数字大小进行排序。如果不指定-n选项，在排序的时候默认按当前域的字符序列进行排序，如下：

<pre>
root@raspberrypi:/home/pi/exercise# cat computer.db|sort -t: -k3n
SumSung:Korea:5400:2009:Q308
HP:China:5600:2010:DM3
Acer:Taiwan:8000:2000:PT210
IdeaPad:China:8000:2007:U450
thinkpad:USA:8000:2007:x60
thinkpad:HongKong:10000:2008:T400
HP:China:12000:2010:NE808
thinkpad:USA:14000:2009:x301
</pre>

### 1.4\.-r选项

-r用于将排序的结果逆向显示，如果要按数值降序可按如下:

<pre>
root@raspberrypi:/home/pi/exercise# cat computer.db|sort -t: -k3nr
thinkpad:USA:14000:2009:x301
HP:China:12000:2010:NE808
thinkpad:HongKong:10000:2008:T400
Acer:Taiwan:8000:2000:PT210
IdeaPad:China:8000:2007:U450
thinkpad:USA:8000:2007:x60
HP:China:5600:2010:DM3
SumSung:Korea:5400:2009:Q308
</pre>

### 1.5\.-u选项

-u用于去除重复选行，在上面文件中加入几个重复行，用-u过滤后如下：

<pre>
root@raspberrypi:/home/pi/exercise# cat computer.db |sort -t: -u
Acer:Taiwan:8000:2000:PT210
HP:China:12000:2010:NE808
HP:China:5600:2010:DM3
IdeaPad:China:8000:2007:U450
SumSung:Korea:5400:2009:Q308
thinkpad:HongKong:10000:2008:T400
thinkpad:USA:14000:2009:x301
thinkpad:USA:8000:2007:x60
</pre>

### 1.6\.-o选项

一般用sort排序后都是将结果直接输出到屏幕或者管道，也可以直接输出到文件，跟IO重定向一个效果。如下:

<pre>
root@raspberrypi:/home/pi/exercise# cat computer.db |sort -t: -u -o com1.db
</pre>

### 1.7\.-c用于测试文件是否已经排好序

刚刚的com1.db是已经排好序的文件，测试如下：

<pre>
root@raspberrypi:/home/pi/exercise# sort -t: -c com1.db
root@raspberrypi:/home/pi/exercise#
</pre>

如果没有排好序结果如下：

<pre>
root@raspberrypi:/home/pi/exercise# sort -t: -c computer.db
sort: computer.db:2: disorder: thinkpad:HongKong:10000:2008:T400
</pre>

### 1.8\.将排好序的两个文件合并成一个前提是两个文件都必须是已经排好序的