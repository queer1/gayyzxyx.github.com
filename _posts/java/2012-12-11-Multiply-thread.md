---
layout: post
title: JAVA多线程
category: java
tags: [java, thread]
keywords: java,thread
description: java多线程
---

在java中实现多线程一般是通过两种手段，一是实现Runnable接口，还有就是继承Thread类，考虑到如果是继承Thread类，不适合具有相同代码的

进程实现资源的共享，如下两个程序所示

<pre class="prettyprint linenums">
public class TempThread extends Thread {
    private int count = 4;
    public void run(){
        while(count>0){
            System.out.println("count:"+count);
            count--;
        }
    }
    public static void main(String args[]){
        TempThread tempThread1 = new TempThread();
        TempThread tempThread2 = new TempThread();
        tempThread1.start();
        tempThread2.start();
    }
}
</pre>

输出结果：
<pre class="prettyprint linenums">
count:4
count:3
count:2
count:1
count:4
count:3
count:2
count:1
</pre>