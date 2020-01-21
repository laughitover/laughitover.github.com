---
layout: post
title: 使用profiler分析dump文件排查内存泄漏
category: interview
---

**现象:** 线上服务一直重启

**原因:** heartbeat每隔5s进行服务探活，连续3次超过1s无响应认为服务不可用，重启服务，通过日志发现服务频繁GC产生大段STW（stop the world）导致服务停顿。

**排查:** 通过Jporfiler进行分析

**安装:** 参看: [Intellij IDEA集成性能分析工具JProfiler](http://www.laughitover.com//practice/2019/12/04/Jprofile.html），安装之后可以单独使用。

### 打开Jprofiler会弹出一个对话框，分析堆转储快照，直接点击"Open a Snapshot"

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/001.png)

### 加载dump文件（文件的后缀要由.dump改成.hprof）

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/002.png)

### 加载dump文件完成,界面如下:

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/003.png)

### 选中占内存最大对象,具体分析，可以点击"size"排序,找到占用最多的对象

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/004.png)

### 查看引用关系

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/005.png)

### 找熟悉的对象（可根据包名前缀查找）

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/006.png)

### 在"Biggest Objects"视图,查看占内存最大对象

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/007.png)

### 点击"show in graph" ,通过图表的方式查看

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/008.png)

### 一级一级展开，可以通过调用链查找到对应的线程栈

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/009.png)

### 最后发现是查数据库一次性返回过多数据，其列表对象就是InspectObject对象(大对象)，占用内存太大，GC无法及时处理,导致内存溢出.

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/jprofilerAnalyzeDump/010.png)