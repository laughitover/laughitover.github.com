---
layout: post
title: Java并发编程之详解工具类CountDownLatch
category: interview
---

CountDownLatch是一个在java1.5被引入同步工具类，它允许一个或多个线程一直等待，直到其他线程的操作执行完后再执行。countdownlatch在Java开发中应用场景及其广泛，同时也是面试中的高频考点。
每一个Java程序员都应该熟练掌握，在本篇文章中，我将会从以下几方面对其进行详细讲解：

## 一、根据源码刨析CountDownLatch工作原理
### 1、实现原理：
 CountDownLatch是通过用其内部类Sync（继承AbstractQueuedSynchronizer）构建一个计数器的方式来实现的，计数器的初始值为线程的数量。每当一个线程完成了自己的任务后，调用countDown()方法计数器的值就会减1。当计数器值减到0时，在闭锁上等待的线程就可以继续执行任务。
 
### 2、源码解析
CountDownLatch类结构
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181220173449661.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NhaWRlMw==,size_16,color_FFFFFF,t_70)
Sync ：CountDownLatch的内部类，Sync继承AbstractQueuedSynchronizer，采用AQS构建同步器。
CountDownLatch(int)：构造器，初始化计数。
await()：当前线程一直等待，直到计数器为0才往下执行。
await(long,TimeUnit):设置当前线程等待的时间，时间到了，不管其它线程是否执行完成。
getCount()：获取当前计数器的值
假设我们创建的：new CountDownLatch(5)。其实也就相当于new Sync(5)，相当于setState(5)。setState我们可以暂时理解为设置一个计数器，当前计数器初始值为5。
tryAcquireShared方法其实就是判断一下当前计数器的值，是否为0了，如果为0的话返回1（返回1的时候，就表明当前线程可以继续往下走了，不再停留在调用countDownLatch.await(）这个方法的地方）。
tryReleaseShared方法就是利用CAS的方式，对计数器进行减一的操作，而我们实际上每次调用countDownLatch.countDown()方法的时候，最终都会调到这个方法，对计数器进行减一操作，一直减到0为止。


#### 2.1、CountDownLatch内部类Sync
```
  /**
    * 使用 AQS 的状态代表计数值
    */
    private static final class Sync extends AbstractQueuedSynchronizer {
        private static final long serialVersionUID = 4982264981922014374L;
        //初始化计数值
        Sync(int count) {
            setState(count);
        }
        //获取当前计数值
        int getCount() {
            return getState();
        }

        //在共享模式下尝试获取状态（计数为0时返回1，否则返回-1）
        protected int tryAcquireShared(int acquires) {
            return (getState() == 0) ? 1 : -1;
        }

        //在共享模式下尝试减量计数，并返回转换后的信号(是否转换到0)
        protected boolean tryReleaseShared(int releases) {
            // Decrement count; signal when transition to zero
            for (;;) {//无限循环
                int c = getState();
                if (c == 0) //当前计数值为0 说明已完成，没有线程在占用，返回false（不需要调用 doReleaseShared()去释放所有await线程）
                    return false;
                int nextc = c-1;// 下一个状态
                if (compareAndSetState(c, nextc))
                    return nextc == 0;//下一个状态nextc成功设置为0，返回true（后面调用 doReleaseShared()去释放所有await线程）；否则返回false（不释放），其它线程再继续减量计数
            }
        }
    }
```

#### 2.2、CountDownLatch(n)构造器

CountDownLatch构造函数调用内部Sync类构造函数，而Sync继承AQS（AbstractQueuedSynchronizer）同步器，利用AQS的state机制使计算值处于同步共享状态。

```
public CountDownLatch(int count) {
    if (count < 0) throw new IllegalArgumentException("count < 0");
    this.sync = new Sync(count);
}
```

#### 2.3、countDownLatch.await()方法

```
public void await() throws InterruptedException {
        sync.acquireSharedInterruptibly(1);
}
```
acquireSharedInterruptibly（）方法是抽象类AbstractQueuedSynchronizer的一个方法，源码：

```
public final void acquireSharedInterruptibly(int arg)
            throws InterruptedException {
        if (Thread.interrupted())
            throw new InterruptedException();
        if (tryAcquireShared(arg) < 0)
            doAcquireSharedInterruptibly(arg);
}
```

首先判断了一下，当前线程是否有被中断，如果没有的话，就调用Sync中的tryAcquireShared(int acquires)方法，如果当前计数器的值不为0，则执行AbstractQueuedSynchronizer类中的方法：doAcquireSharedInterruptibly(arg)。

```
    private void doAcquireSharedInterruptibly(int arg)
        throws InterruptedException {
        final Node node = addWaiter(Node.SHARED);
        boolean failed = true;
        try {
            for (;;) {
                final Node p = node.predecessor();
                if (p == head) {
                    int r = tryAcquireShared(arg);
                    if (r >= 0) {
                        setHeadAndPropagate(node, r);
                        p.next = null; // help GC
                        failed = false;
                        return;
                    }
                }
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    throw new InterruptedException();
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
```

这个时候，当前线程就会进入了一个死循环当中，在这个死循环里面，会不断的进行判断，通过调用tryAcquireShared方法，判断计数器的值是否为0（为0的时候，其实就是我们调用了足够多次数的countDownLatch.countDown（）方法的时候），如果是为0的话，tryAcquireShared就会返回1，代码会继续执行，然后跳出了循环，也就不再“阻塞”当前线程了。
>说是在不停的循环，其实也并非在不停的执行for循环里面的内容，因为在后面调用parkAndCheckInterrupt（）方法时，是会调用方法LockSupport.park(this)来禁用当前线程。
 
 #### 2.4、countDownLatch.countDown()方法

```
	public void countDown() {
	        sync.releaseShared(1);
	}　
```

```
    public final boolean releaseShared(int arg) {
        if (tryReleaseShared(arg)) {
            doReleaseShared();
            return true;
        }
        return false;
    }
```

线程调用此方法进行减1操作（实际是调用CountDownLatch内部类Sync的tryReleaseShared(arg)方法）。当count比0大，调用此方法进行减1，当count减为0时，调用doReleaseShared()方法释放所有等待当线程。
>注：CountDownLatch底层采用AQS构建同步器，是共享锁机制，所以减1操作通过 CAS 和 volatile 保证了原子性和可见性。

## 二、CountDownLatch的应用实例
CountDownLatch的应用场景有很多，比如多线程下载，应用程序启动类在外部应用启动之后启动等。一般可以分为以下三中场景。
### 1.	实现最大的并行性，有时我们想同时启动多个线程，实现最大程度的并行性。这个“同时”的保证就可以通过CountDownLatch来实现。比如我们要模拟马拉松比赛，发号枪响了之后所有运动员同时出发：

```
    public static void marathon() {
        long startTime = System.currentTimeMillis();
        // 初始化计数器为1
        CountDownLatch countDownLatch = new CountDownLatch(1);
        for (int i = 0; i < 30; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try{
                        // 线程等待
                        countDownLatch.await();
                        // 模拟耗时操作
                        Thread.sleep(3000);
                        long endTime=System.currentTimeMillis();
                        System.out.println(Thread.currentThread().getName() +"到达终点,完成时间: "+LocalDateTime.now()+", 用时: "+(endTime-startTime)+"ms.");
                    }catch(Exception e){
                        e.printStackTrace();
                    }
                }
            }).start();
        }
        System.out.println("30人参加马拉松比赛,同时出发时间:"+LocalDateTime.now());
        countDownLatch.countDown();
    }
```
执行结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181221104216295.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NhaWRlMw==,size_16,color_FFFFFF,t_70)
### 2.	开始执行前等待其它线程完成各自任务，这种场景应用最为广泛，例如应用程序启动类要确保在处理用户请求前，所有外部系统都已经启动和运行了。在比如开启多个线程分块下载一个大文件，每个线程只下载固定的一截，最后由另外一个线程来拼接所有的分段，那么这时候我们可以考虑使用CountDownLatch来控制并发，使得拼接的线程放在最后执行。这里我们通过一个简单场景来模拟一下，公司5个董事开会，需要所有董事全部到达会议才开始：

```
    public static void directorMeeting() {
        int threadCount = 5;
        final CountDownLatch latch = new CountDownLatch(threadCount);
        for (int i = 0; i < threadCount; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println("董事[" + Thread.currentThread().getName() + "]开始出发");
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("董事  {" + Thread.currentThread().getName() + "}  已到达");
                    latch.countDown();
                    System.out.println("还有：" + latch.getCount() + "个没到达");
                }
            }).start();
        }
        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("5个董事全部到达！开始开会");
    }
```
与CountDownLatch的第一次交互是主线程等待其他线程。主线程必须在启动其他线程后立即调用CountDownLatch.await()方法。这样主线程的操作就会在这个方法上阻塞，直到其他线程完成各自的任务。
运行结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181221104746490.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NhaWRlMw==,size_16,color_FFFFFF,t_70)
>思考：上个例子中的马拉松比赛中，计算成绩要在所有人完成比赛之后在进行，也可以通过CountDownLatch来实现，大家可以自己模拟一下。
### 3.	死锁检测：这种场景应用较少，下例用CountDownLatch的检测死循环。
```
public class CountDownLatchForInfinitLoop {

    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 100000; i++) {
            loopOper();
            System.out.println("Loop times: "+i);
        }
    }

    public static void loopOper() throws InterruptedException{
        CountDownLatchForInfinitLoop test = new CountDownLatchForInfinitLoop();
        CountDownLatch latch = new CountDownLatch(2);
        HashMap map = new HashMap(1);
        Thread t1 = new Thread(test.new MapOper(latch,map));
        Thread t2 = new Thread(test.new MapOper(latch,map));
        t1.start();
        t2.start();
        latch.await();
    }

    public class MapOper implements Runnable {
        CountDownLatch latch ;
        HashMap map;
        public MapOper(CountDownLatch latch, HashMap map) {
            this.latch = latch;
            this.map = map;
        }
        public void run() {
            for (int i = 0; i < 50000; i++) {
                try {
                    map.put(String.valueOf(i),i);
//                    System.out.println(Thread.currentThread().getName()+"i:"+i);
                } catch (Exception e) {
                    System.out.println("put value exception");
                }
            }
            latch.countDown();
        }
    }

}
```
运行结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181221105945812.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NhaWRlMw==,size_16,color_FFFFFF,t_70)
到箭头所指时会卡住，CountDownLatch在本例子的作用是，每次Loop都等线程执行完了，再执行下一次loop，如果某一次出现死循环，则countDown()不会被执行，loop就会被”阻塞“在某次循环。
## 三、CountDownLatch常见的面试题
### 1、介绍一下CountDownLatch工作原理?
答案见此文 1.1实现原理
### 2、CountDownLatch 和CyclicBarrier的区别?
答：
（1）CountDownLatch的计数器只能使用一次。而CyclicBarrier的计数器可以使用reset() 方法重置。所以CyclicBarrier能处理更为复杂的业务场景，比如如果计算发生错误，可以重置计数器，并让线程们重新执行一次。
（2）CyclicBarrier还提供其他有用的方法，比如getNumberWaiting方法可以获得CyclicBarrier阻塞的线程数量。isBroken方法用来知道阻塞的线程是否被中断。
（3）CountDownLatch会阻塞主线程，CyclicBarrier不会阻塞主线程，只会阻塞子线程。
### 3、CountDownLatch的使用场景?
答案见此文 二、CountDownLatch的应用实例
### 4、CountDownLatch 类中主要的方法?
答案见此文 1.2源码解析


参考:
 http://www.importnew.com/15731.html
https://blog.csdn.net/xlgen157387/article/details/78218736
https://blog.csdn.net/xzongyuan/article/details/72669996
https://blog.csdn.net/zhixinhuacom/article/details/78838922
https://blog.csdn.net/figo0423/article/details/80604921


