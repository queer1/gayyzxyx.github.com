---
layout: post
title: JAVA多线程
category: java
tags: [java, thread]
keywords: java,thread
description: java多线程
---

在java中实现多线程一般是通过两种手段，一是实现`Runnable`接口，还有就是继承`Thread`类，考虑到如果是继承`Thread`类，不适合具有相同代码的进程实现资源的共享，如下两个程序所示

1.继承`Thread`

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

2.实现`Runnable1`接口
<pre class="prettyprint linenums">
	public class TempThreadRun implements Runnable {
	    private int count = 7;
	    public void run(){
	        for(int  i = 0;i < 100;i++)
	            if(count>0){
	                System.out.println(Thread.currentThread().getName()+"count:"+count--);
	            }
	    }
	    public static void main(String args[]){
	        TempThreadRun tempThreadRun = new TempThreadRun();
	        new Thread(tempThreadRun,"A").start();
	        new Thread(tempThreadRun,"B").start();
	        new Thread(tempThreadRun,"C").start();
	    }
	}
</pre>

输出结果：

<pre class="prettyprint linenums">
Acount:7
Acount:4
Acount:3
Acount:2
Acount:1
Ccount:5
Bcount:6
</pre>
