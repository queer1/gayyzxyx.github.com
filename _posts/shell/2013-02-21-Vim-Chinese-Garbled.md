---
layout: post
title: vim在windows下中文乱码
category: shell
tags: [shell]
keywords: vim,shell
---

在`windows`下用`vim`编辑带中文字符的文件时会出现中文乱码问题，这是只需要编辑`vim`安装目录下`_vimrc`，加入以下两行：

	let &termencoding=&encoding
	set fileencodings=utf-8,gbk,ucs-bom,cp936