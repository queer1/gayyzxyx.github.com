---
layout: post
title: python中关于string.maketrans()和translate
category: python
tags: [maketrans, translate, python]
keywords: python, maketrans, translate
---

先看string.maketrans的定义

	def maketrans(frm, to): # real signature unknown; restored from __doc__
	    """
	    maketrans(frm, to) -> string
	    
	    Return a translation table (a string of 256 bytes long)
	    suitable for use in string.translate.  The strings frm and to
	    must be of the same length.
	    """
	    return ""

意思是说创建一个转义表(长度为256字节)，在translate的时候根据转义表的内容重新转义字符串内容，并且frm和to的长度相等，并且这个转移模板是给translate用，translate的定义如下

	def translate(self, table, deletechars=None): # real signature unknown; restored from __doc__
	        """
	        S.translate(table [,deletechars]) -> string
	        
	        Return a copy of the string S, where all characters occurring
	        in the optional argument deletechars are removed, and the
	        remaining characters have been mapped through the given
	        translation table, which must be a string of length 256 or None.
	        If the table argument is None, no translation is applied and
	        the operation simply removes the characters in deletechars.
	        """
	        return ""

意思是说返回字符串S的copy t,并且里面的字符串都是除掉参数[,deletechars]中的字符串后的新字符串，t中内容根据转义表相匹配。光看字面意思有点抽象，写段代码试验

	import string
	s = 'i am a good boy'
	t = string.maketrans('abc','ABC')
	print(s.translate(t))

将‘i am a good boy’中出现的abc换成大写的ABC，结果如下：

	i Am A good Boy

可以在translate中增加输出过滤参数，比如过滤掉‘am

	import string
	s = 'i am a good boy'
	t = string.maketrans('abc','ABC')
	print(s.translate(t,'am'))

得到过滤后的字符串这样：

	i   good Boy

如果string.maketrans()带上空参数会是怎样呢？

	import string
	s = 'i am a good boy'
	t = string.maketrans('','')
	print(s.translate(t,'am'))

output:

	i   good boy

其作用跟直接删除参数`[,deletechars]`中出现字符一样效果

接下来一个用一个例子完结，这是[pythonchallenge](http://www.pythonchallenge.com/pc/def/map.html)上一个有意思的题目。给我们一个字符串，我们将每个字符向后位移两个字符的距离，如果是y位移两个后就变成了a,如果是z位移两个后就变成了b，以此类推。此种问题就可以借助string.maketrans(from,to)完成，from是原256个字符串的模板，目标模板是`from[2:]+from[:2]`，如abcd位移后是cdab

	s = """g fmnc wms bgblr rpylqjyrc gr zw fylb. rfyrq ufyr amknsrcpq ypc dmp. bmgle gr gl zw fylb gq
	glcddgagclr ylb rfyr'q ufw rfgq rcvr gq qm jmle. sqgle qrpgle.kyicrpylq() gq pcamkkclbcb. lmu
	ynnjw ml rfc spj."""
	l=string.lowercase
	t=string.maketrans(l,l[2:]+l[:2])
	news = s.translate(t)
	print(news)

output:

	i hope you didnt translate it by hand. thats what computers are for. doing it in by hand is
	inefficient and that's why this text is so long. using string.maketrans() is recommended. now
	apply on the url.



