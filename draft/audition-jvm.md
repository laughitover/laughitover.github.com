jvm常见命令
一、jps：输出jvm中运行的进程状态信息 -l jar全限名
二、jstack：查看某个java进程内的线程栈信息
1、top 找到cpu最高的进程 jps确认
2、ps -mp pid -0 THREAD tid time 找到最耗时的线程
3、printf “%x\n” tid 线程号转成16进制
4、jstack pid | 隔热片 tid（16进制） 定位代码
三、jmap：（memory map）查看堆内存使用情况
1、-heap pid 堆内存使用情况，gc算法，堆配置
2、-dump 到文件
四、jstat：jvm统计监测工具
jstat -gc pid 250 4
五、jhap（heap Analysis Tool）
