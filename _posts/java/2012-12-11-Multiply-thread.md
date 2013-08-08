---
layout: post
codes: [java]
title: JAVA多线程
category: java
tags: [java, thread]
keywords: java,thread
description: java多线程
---

在java中实现多线程一般是通过两种手段，一是实现`Runnable`接口，还有就是继承`Thread`类，考虑到如果是继承`Thread`类，不适合具有相同代码的进程实现资源的共享，如下两个程序所示

继承`Thread`

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

输出结果：

	count:4
	count:3
	count:2
	count:1
	count:4
	count:3
	count:2
	count:1

实现`Runnable`接口

	public class TempThreadRun implements Runnable {
		private int count = 7;
		public void run(){
		for(int  i = 0;i &lt; 100;i++)
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

输出结果：

	Acount:7
	Acount:4
	Acount:3
	Acount:2
	Acount:1
	Ccount:5
	Bcount:6

同时在使用Runnable接口的时候，还可以避免java的单继承限制。使用信号灯，还可以实现线程间的同步，借助Object类，实现线程间的等待与唤醒，如我们要得到如下字符串A12B34C56....Z5152，我们可以创建两个线程，分别输出字符串和数字。

分析：
	1.创建两个线程`LetterThread`和`NumberThread`分别用于控制字母和数字的输出
	2.为了先输出一个字母然后输出两个数字，可以先让`LetterThread`处于等待状态，`NumberThread`唤醒`LetterThread`进行输出工作，此时`NumberThread`处于等待状态
	3.`LetterThread`输出后，唤醒`NumberThread`进行数字的输出，然后`NumberThread`再次唤醒`LetterThread`，如此循环，便可以得到所需字符串

`LetterThread`的实现：

	public class LetterThread implements Runnable {
	    private Object _lock;
	    LetterThread(Object lock) {
	        _lock = lock;
	    }
	    public void run() {
	        synchronized (_lock) {
	            for (int i = 0; i &lt; 26; i++) {
	                try {
	                    _lock.wait();
	                } catch (InterruptedException ie) {
	                    ie.printStackTrace();
	                }
	                System.out.print((char) ('A' + i));
	                _lock.notify();
	            }
	        }
	    }
	}


`NumberThread`的实现：

	public class NumberThread implements Runnable {
	    private Object _lock;
	    public NumberThread(Object lock) {
	        _lock = lock;
	    }
	    public void run() {
	        try {
	            synchronized (_lock) {
	                for (int i = 0; i &lt; 26; i++) {
	                    _lock.notify();
	                    _lock.wait();
	                    System.out.print((2 * i + 1) + "" + (2 * i + 2));
	                }
	                _lock.notify();
	            }
	        } catch (InterruptedException ie) {
	            ie.printStackTrace();
	        }
	    }
	    public static void main(String args[]) {
	        Object lock = new Object();
	        Thread letterThread = new Thread(new LetterThread(lock));
	        Thread numberThread = new Thread(new NumberThread(lock));
	        letterThread.start();
	        numberThread.start();
	    }
	}


结果如下：

	A12B34C56D78E910F1112G1314H1516I1718J1920K2122L2324M2526N2728O2930P3132Q3334R3536S3738T3940U4142V4344W4546X4748Y4950Z5152