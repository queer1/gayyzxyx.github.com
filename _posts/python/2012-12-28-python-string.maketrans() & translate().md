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

