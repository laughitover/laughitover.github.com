---
layout: post
title: 使用profiler分析dump文件排查内存泄漏
category: interview
---

## 现象: 线上服务一直重启

## 原因: heartbeat每隔5s进行服务探活，连续3次超过1s无响应认为服务不可用，重启服务，通过日志发现服务频繁GC产生大段STW（stop the world）导致服务停顿。

## 排查: 通过Jporfiler进行分析

## 安装: [Intellij IDEA集成性能分析工具JProfiler](http://www.laughitover.com//practice/2019/12/04/Jprofile.html)，可以单独使用。

## 一、打开Jprofiler

双击Jprofiler.exe,启动之后会弹出一个对话框，因为要分析堆转储快照，直接点击"Open a Snapshot"

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/001.png)

## 二、加载dump文件

生成dump文件方法参考：[jvm 基础之解析堆dump文件]( http://www.laughitover.com//interview/2020/01/13/jvm-dump.html ) 生成的dump文件的后缀是.dump，要改成.hprof，才能用jprofiler打开

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/002.png)

## 三、加载dump文件

加载完成，对占内存最大对象进行分析，可以点击"size"排序

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/004.png)

## 四、查看引用关系

- Merged outcoming references：查看它所引用的对象

- Merged incoming references：查看它被哪些对象引用

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/005.png)

## 五、找到熟悉的对象

可根据包名前缀查找，一般可根据com.公司的包名 进行查找

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/006.png)

## 六、在"Biggest Objects"视图,

右键查看占内存最大对象，

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/007.png)

点击"show in graph" ,通过图表的方式查看

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/008.png)

## 七、在graph试图查看调用链

一级一级展开，可以通过调用链查找到对应的线程栈

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/009.png)

## 八、得出结论

通过对应线程栈找到内存泄漏的代码，发现是查数据时一次性返回过多数据，其列表对象就是InspectObject对象(大对象)，占用内存太大，GC无法及时处理,导致内存溢出.

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/011.png)

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/012.png)
