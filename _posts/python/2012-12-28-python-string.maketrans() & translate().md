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