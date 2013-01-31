---
layout: post
title: shell神器putty密钥自动登录
category: shell
tags: [shell, putty, ssh]
keywords: shell, putty, ssh
---

### 情形一：windows下通过客户端如putty登录服务器

在远程端操纵linux服务器的时候，putty、SSH Secure Shell都是很爽的连接工具。如果每次连接服务器的时候都要输入IP、USERNAME、PASSWORD将会是一个非常耗时间的过程，一点不人性化，用[puttygen](http://the.earth.li/~sgtatham/putty/0.62/x86/puttygen.exe)生成服务端的public key加密数据返回给客户端，然后客户端用private-key进行解密后传输给服务器进行验证。
1.运行puttygen生成publickey复制备用，然后将privatekey存至本地某一目录。
2.用putty登录上服务器，看`/root`下面是否有`.ssh`目录，没有则`mkdir .ssh`
3.接着'cat <<EOF>> authorized_keys '，输入刚刚生成的公钥，接着`EOF`结束（如果已经有`authorized_keys`则直接追加公钥在最后面OK）
4.在putty的hostname输入服务器IP，默认端口22，在saved sessions中输入服务器名称然后save
5.connection->Data->login details中输入登录用户名，connectin->SSH->Auth中选择刚才保存的私钥
6.回到session,点击save,再登录就OK，再也不用输入用户名密码登录。

### 情形二：shell下通过SSH登录另一台服务器

我现在有两台服务器：192.168.115.128、192.168.115.129，此时我已经通过在putty上登陆128了，在shell命令行下，同样可以直接用ssh命令登录到129服务器，类似的需要公钥的加密和私钥的解密，过程如下：
1.`ssh-keygen`生成公钥和私钥，此时可以在`root/.ssh`下看到`id_rsa`和`id_rsa.pub`，分别对应本地的私钥和服务器上的公钥
2.`ssh-copy-id root@192.168.115.129`，将公钥传到远程主机的host上面，输入远程主机的密码，OK这下就不需要密码登录了
3.`ssh 192.168.115.129`,看着下是不是已经登录到远程服务器了，`ifconfig`查看下IP，可以看到`IP address for eth0:192.168.115.129`，`exit`退出登录
