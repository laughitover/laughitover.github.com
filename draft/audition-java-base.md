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

底层基于volatile和cas实现
monitorenter monitorexit 计数器

设置线程数
1、