---
layout: post
title: linux中ll -h 命令一个奇怪的现象
category: interview
---

total统计的是所有文件大小，但是这个值却小于某个文件的大小
 ![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/falseDisk/01.png)
 
 
原因可能是 文件空洞 和 稀疏文件。

这是日志文件，所以文件空洞的可能性更大；应该是由于写日志的时候，移动文件指针到大于文件末尾的位置，然后写入数据，导致中间没有写入的部分全是0，
这部分空间占用磁盘由使用的文件系统决定，从结果来看，它是没有占用磁盘空间的。

而 ll 的结果中，total 指占用的的磁盘大小，下面的 4.7T 是这个文件的大小