---
layout: post
title: 给树莓派添加显示屏
category: python
tags: [python]
keywords: raspberry-pi
---

给树莓派添加一个外部的黑白屏用于显示运行状态，包括温度，内存，cpu使用，ip地址等等，屏幕用诺基亚5100的拆机屏。需要用到一个函数库，在Raspberry Pi的讨论区中已经有人共享了成果[nokimagpi.tar.gz](http://www.raspberrypi.org/phpBB3/download/file.php?id=2239&sid=54ab6c13f737d4df57d295afa8bf3752)。由于这个程序用到一些modules，需要装一些环境支持:

<pre>
sudo apt-get install python-dev
sudo apt-get install python-pip
sudo pip install wiringpi
sudo pip install spidev
sudo pip install pil
</pre>

5100显示屏的针脚定义从左到右依次是：RST,CE,DC(D/C),Din(DNK),Clk(SCLK),Vcc,BL(LED),Gnd,总共8根线，将其连接到树莓派的gpio口，对应关系如下(将pi的网口端正对自己，从左往右，依次向下分别为pin 1 2 3...)：

<pre>
RST ←→ pin 11
CE (SCE) ←→ pin 24
DC (D/C)  ←→ pin 15
Din (DNK) ←→ pin 19
Clk (SCLK) ←→ pin 23
Vcc  ←→ pin 1
BL(LED) ←→ pin 12
Gnd  ←→ pin 6
</pre>

接下来写脚本调用`nokiaSPI.py`控制屏幕输出，常用的方法如下：

<pre>
nokia.cls()：清除显示
nokia.gotoxy(x, y)：將游标移动到 x, y 指定的位置。
nokia.text("英文字串")：在目前的屏幕中显示英文字符。
nokia.led(0)：关闭背光
</pre>

需要用到显示相关信息的方法如下，获取内存和CPU占用需要用到psutil包：

<pre>
#获取IP地址
def getIp():
    ipAdd = commands.getstatusoutput("ifconfig|grep 'inet addr:'|grep -v '127.0.0.1'|awk '{print $2}'|awk -F':' '{print $2}'")[1]
    return ipAdd

#获取温度信息
def getTempurature():
	tempurature = str(string.atoi(commands.getstatusoutput("cat /sys/class/thermal/thermal_zone0/temp")[1])/1000.0)
	return tempurature

#获取CPU占用
def getCpuUseage():
	cpu = "cpu:"+str(psutil.cpu_percent(0.1))+"%"
    return cpu

#获取内存占用情况
def getMem():
    phymem = psutil.phymem_usage()
    buffers = getattr(psutil, 'phymem_buffers', lambda: 0)()
    cached = getattr(psutil, 'cached_phymem', lambda: 0)()
    used = phymem.total - (phymem.free + buffers + cached)
    line = "Mem:%s/%s" % (
        str(int(used / 1024 / 1024)) + "M",
        str(int(phymem.total / 1024 / 1024)) + "M"
    )
    return line

#获取开机运行时间
def getUpTime():
    time = commands.getstatusoutput("uptime|awk -F',' '{print $1}'|awk '{print $3}'")[1]
    return time
</pre>

运行后的效果
![sign](/assets/blog/pi/screen.jpg)