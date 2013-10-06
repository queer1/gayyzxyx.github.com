---
layout: post
title: 文件同步
category: python
tags: [python]
keywords: synchronise
---

有时候需要用到文件同步将本地文件与远程文件保持一致，可以是全量的，也可以是增量的，于是用py简单撸了下。简单来说就是遍历当前文件夹，如果当前是文件则检测目标文件夹中是否存在该文件，不存在就复制；如果检测到的是文件夹，则递归检测子文件夹，核心如下：

<pre>
class Syn:
    def __init__(self, dPath):
        self.sPath = os.getcwd()
        self.dPath = dPath
        self.absFolder = ''

    def copyFile(self, path):
        for i in os.listdir(path):
            if os.path.isfile(os.path.join(path, i)):
                sourceFile = os.path.join(os.path.join(self.sPath, self.absFolder), i)
                targetFile = os.path.join(os.path.join(self.dPath , self.absFolder), i)
                if not (os.path.exists(targetFile) and os.path.getsize(targetFile) == os.path.getsize(sourceFile)):
                    open(targetFile, "wb").write(open(sourceFile, "rb").read())
            else:
                targetPath = os.path.join(os.path.join(self.dPath, self.absFolder), i)
                if not os.path.exists(targetPath):
                    self.absFolder = os.path.join(self.absFolder, i)
                    os.makedirs(targetPath)
                    self.copyFile(os.path.join(self.sPath, self.absFolder))
                    self.absFolder = os.path.split(self.absFolder)[0]
                else:
                    self.absFolder = os.path.join(self.absFolder, i)
                    self.copyFile(os.path.join(self.sPath, self.absFolder))
                    self.absFolder = os.path.split(self.absFolder)[0]
</pre>

对了全量同步，只需要在同步前把目标文件夹给删除掉。在程序运行前带入参数。

<pre>
	if __name__=="__main__":
    target = sys.argv[2].decode("utf-8").encode("gbk")
    if sys.argv[1]=="all":
        for file in os.listdir(target):
            try:
                if os.path.isfile(os.path.join(target, file)):
                    os.remove(os.path.join(target, file))
                else:
                    os.system('rd /S /Q %s'% os.path.join(target, file))
            except Exception,e:
                print e, file
    syn = Syn(target)
    syn.copyFile(os.getcwd())
    print "success"
</pre>

核心代码不超过20行，但是为日常应用带来了很大的方便。