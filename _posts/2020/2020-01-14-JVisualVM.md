---
layout: post
title: JVisualVM
category: interview
---



JVisualVM 简介

 jvisualvm是JDK自带的Java性能分析工具，在JDK的bin目录下，文件名就叫jvisualvm.exe。 双击打开即可使用， jvisualvm可以监控本地、远程的java进程，实时查看进程的cpu、堆、线程等参数，对java进程生成dump文件，并对dump文件进行分析。 

很多插件   Java VisualVM的最大好处是可通过安装Visual GC插件来分析**GC（Gabage Collection）**趋势、内存消耗详细状况。 

最常用的场景是 从服务器上dump下来文件也可以直接扔给jvisualvm来分析 







对于Java8来说，一般堆内存的初始容量为物理内存大小的1/64， 最大内存不超过物理内存的1/4或1G.

initial heap size

Larger of 1/64th of the machine's physical memory on the machine or some reasonable minimum. Before Java SE 5.0, the default initial heap size was a reasonable minimum, which varies by platform. You can override this default using the -Xms command-line option.

maximum heap size

Smaller of 1/4th of the physical memory or 1GB. Before Java SE 5.0, the default maximum heap size was 64MB. You can override this default using the -Xmx command-line option.

Note: The boundaries and fractions given for the heap size are correct for Java SE 5.0. They are likely to be different in subsequent releases as computers get more powerful.

