---
layout: post
title: 使用nginx解决BOTTLE单例
category: shell
tags: [bottle, nginx]
keywords: bottle, nginx, python
---

bottle的单例访问让人蛋疼，本来就不高的并发，为了解决这种问题想到了nginx代理多端口转发。首先在程序里面将端口改成动态传入,在main.py中修改：
	
	import sys
	import string
	args = sys.argv
	#默认args[0]是文件的名字 args[1],[2]等依次是后面跟着的参数
	port = string.atoi(args[1])  
	. 
	.
	.
	run(app,host=host,port=port)

启动实例的端口由shell脚本带入，需要再次写以下脚本，表示根据shell带进的入参，依次启动实例：

	 #!/bin/bash
 	 
 	 cd /home/pi/gayyzxyx-manage/gayyzxyx-manage-8080/
 	 
 	 if [ $# = 0 ];then
 	     echo "pls input ports"
 	     exit 1
 	 fi
 	 
 	 file=main.py
 	 
 	 if [ ! -f $file ]; then
 	     echo "main.py does't exist"
 	     exit 1
 	 fi
 	 for port in "$@"
 	 do
 	     python main.py $port> /dev/null 2>&1 &
 	 done

接着修改nginx动态代理如下：

	upstream bottle{
            server 127.0.0.1:8080;
            server 127.0.0.1:8081;
            server 127.0.0.1:8082;
            server 127.0.0.1:8083;
        }
        charset        utf-8;
        server {
            listen       80;
            server_name  localhost;
    
            #charset koi8-r;
    
            #access_log  logs/host.access.log  main;
    
            location / {
                #root   html;
                #index  index.html index.htm;
                proxy_pass              http://bottle;
                proxy_set_header        Host             $host;
                proxy_set_header        X-Real-IP        $remote_addr;
                proxy_set_header        X-Forwarded-For  $proxy_add_x_forwarded_for;
            }
            .
            .
            .
           }

其中在upstream中加入需要启动实例的ip:port，在上面的shell脚本中写了几个就需要在这里配置几个，至此基本上配置完成。先通过./xx.sh 8080 8081 8082 8083先启动实例，查看进程会发现如下：

	root@raspberrypi:/usr/local/nginx/1.4.1# ps -ef | grep python | grep -v grep
	root     19567     1  0 10:37 pts/0    00:00:02 python main.py 8080
	root     19568     1  0 10:37 pts/0    00:00:02 python main.py 8081
	root     19569     1  0 10:37 pts/0    00:00:02 python main.py 8082
	root     19570     1  0 10:37 pts/0    00:00:04 python main.py 8083

接着启动nginx,在远程电脑上访问192.168.1.105，可以发现每个实例都有转发内容