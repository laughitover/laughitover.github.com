###wait和sleep区别
1、wait是Object对象的方法作用于对象本身，sleep是Thread的方法，作用于当前线程
2、wait唤醒需要调用notify或notifyAll方法，或设置超时时间，sleep唤醒需要设置超时时间或者调用interrupt方法
3、wait释放锁等等资源，sleep不释放锁
4、作用不同，wait用于线程通信，sleep控制自身流程

线程状态间的转化图

synchronized和lock区别 reentrantLock
1、synchronized是java关键字，lock是juc下的一个接口
2、synchronized自动释放，lock需要手动释放（finally）
3、synchronized不能中断，lock提供很多方法实现可中断
4、synchronized不能判断是否获得锁，lock可以
5、synchronized是非公平锁，lock可实例化为公平锁
6、synchronized是悲观锁，lock是乐观锁
7、synchronized适用于少量同步代码的情况，lock适用于大量的同步代码

synochronized锁升级jdk1.6
无锁-》偏向锁-》轻量级锁-》重量级锁
锁存在于是对象头 ThreadId为空表示可加偏向锁，cas判断是否是当前线程，不是升级成轻量级锁，
自适应cas（默认10次）preLockspin lockRecord指向栈中锁记录的指针
指向重量级锁的指针
对象的hashcode 线程

大部分场景存在两个以上的线程竞争，开启偏向锁反而会获取锁的资源消耗，所以可以通过jvm参数UseBiase

底层基于volatile和cas实现
monitorenter monitorexit 计数器

设置线程数 cpu密集型 io密集型
1、cpu密集型任务
一般cpu核数+1，尽量使用较小的线程池，因为cpu密集型任务cpu使用率高，开的多，会增加线程上下文切换的开销
2、io密集型
a、cpu核数*2 使用率不高，等待io的时候处理别的任务，充分利用cpu
b、线程等待时间越长，线程数越多
（线程等待时间/线程cpu使用时间+1）*cpu数
cpu等待1.5s 实际处理0.5s
（0.5+1.5）/0.5*8=32
c、混合型


集合框架体系图

HashMap线程不安全
HashMap在put数据的时候，插入元素超过容量，触发扩容rehash，重新将原数组的内容hash到新的扩容数组中，
在多线程环境下，同时put元素，如果hash值相同，可能出现在同一数组下用链表表示，造成闭环，导致get时出现死循环

计算键的hash值
```java
static final int hahs(Object key){
    int h;
    return (key==null)?0:(h=key.hashCode())^(h>>>16);
}
```
1、防止重写hash，导致分布性不足，导致hash冲突高，通过异或运算让hash更复杂
hashCode是int类型32位 高16位 低16位 右移16位
2、为了让高位参与计算，让高位数据与低位数据进行异或，以此加大低位信息的随机性，
变相让高位数据参与到计算中

红黑树
1、根是黑色
2、所有叶子节点都是黑色
3、每个红色节点必须有两个黑色子节点（红节点不连续）
4、从任一节点到其每个叶子节点的所有路径都包含相同数目的黑色节点

