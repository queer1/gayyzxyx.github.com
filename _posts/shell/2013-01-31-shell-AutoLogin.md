---
layout: post
title: shell神器putty密钥自动登录
category: shell
tags: [shell, putty, ssh]
keywords: shell, putty, ssh
---

在远程端操纵linux服务器的时候，putty、SSH Secure Shell都是很爽的连接工具。如果每次连接服务器的时候都要输入IP、USERNAME、PASSWORD将会是一个非常耗时间的过程，一点不人性化，用[puttygen](http://the.earth.li/~sgtatham/putty/0.62/x86/puttygen.exe)生成服务端的public key加密数据返回给客户端，然后客户端用private-key进行解密后传输给服务器进行验证。
1.运行puttygen生成publickey复制备用，然后将privatekey存至本地某一目录。
2.用putty登录上服务器，看`/root`下面是否有`.ssh`目录，没有则`mkdir .ssh`
3.接着'cat &lt&ltEOF>> authorized_keys '，输入刚刚生成的公钥，接着`EOF`结束（如果已经有`authorized_keys`则直接追加公钥在最后面OK）
4.在putty的hostname输入服务器IP，默认端口22，在saved sessions中输入服务器名称然后save
5.connection->Data->login details中输入登录用户名，connectin->SSH->Auth中选择刚才保存的私钥
6.回到session,点击save,再登录就OK，再也不用输入用户名密码登录。