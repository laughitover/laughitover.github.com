---
layout: post
title: 面试系列之单例模式
category: interview
---

## 一、单例模式
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;单例模式是最常用的设计模式之一，常常在面试中被面试官要求手写。  

### 1、什么是单例模式
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;保证一个类仅有一个实例，并提供一个访问它的全局访问点。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;比如我们在系统启动时，需要加载一些公共的配置信息，对整个应用程序的整个生命周期中都可见且唯一，这时需要设计成单例模式。如：spring容器，session工厂，缓存，数据库连接池等等。
### 2、如何保证实例的唯一
 	由类本身进行实例化
 	保证实例化一次
 	对外提供获取实例的方法
 	线程安全
## 二、单例模式的Java实现
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;衡量单例模式实现方式好坏的三个标准：**懒加载，线程安全，效率高**。
### 1、饿汉式
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; “因为饿，所以不能等”，在定义类的静态私有变量同时进行实例化。
```
    public class Singleton {
        private static final Singleton singleton = new Singleton();
        private Singleton() {
        }
        public static Singleton getInstance() {
            return singleton;
        }
    }
```
优点：简单，在类装载的时候就完成实例化，避免了线程同步问题；线程安全。
缺点：没有起到lazy loading的效果；
> 也可以将类实例化的过程放在了静态代码块中，效果相同  

### 2、懒汉式  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; “比较懒，等用的时候才去实例化”，延迟加载。可分为**懒汉式**和**线程安全的懒汉式**，区别是关键字synchronized的使用。
```
    public class Singleton {
        private static Singleton singleton = null;
        private Singleton() {
        }
        public synchronized static Singleton getInstance() {
            if (singleton == null) {
                singleton = new Singleton();
            }
            return singleton;
        }
    }
```
优点：懒加载，线程安全。
缺点：每次获取实例都要加锁，耗费资源，其实只要实例已经生成，以后获取就不需要再锁了。
### 3、双重检查锁
```
    public class Singleton {
        private static Singleton singleton;
        private Singleton() {
        }
        public static Singleton getInstance() {
            if (singleton == null) {
                synchronized (Singleton.class) {
                    if (singleton == null) {
                        singleton = new Singleton();
                    }
                }
            }
            return singleton;
        }
    }
```
优点：懒加载；线程安全；进行双重检查，保证只在实例未初始化前进行同步，效率较高。
### 4、静态内部类
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 静态内部类方式在Singleton类被装载时并不会立即实例化，而是在需要实例化时，调用getInstance方法，才会装载SingletonHolder 类，从而完成Singleton的实例化，实现了懒加载。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 类的静态属性只会在第一次加载类的时候初始化，所以在这里，JVM帮助我们保证了线程的安全性，在类进行初始化时，别的线程是无法进入的。
```
    public class Singleton {
        private Singleton() {
        }
        private static class SingletonHolder {
            private static final Singleton singleton = new Singleton();
        }
        public static Singleton getInstance() {
            return SingletonHolder.singleton;
        }

    }
```
优点：懒加载；线程安全；同时避免了同步带来的性能损耗，效率高。
### 5、枚举
```
    public enum Singleton{
        INSTANCE;
        public void otherMethods(){
            System.out.println("Something");
        }
    }
```
Effective Java作者Josh Bloch 提倡的方式，神写法，天然线程安全，可以自由序列化，并且可防止反射生成实例。

参考：  
https://www.cnblogs.com/jiangkuan/p/6031040.html  
https://www.cnblogs.com/zhaoyan001/p/6365064.html  
https://www.cnblogs.com/cielosun/p/6582333.html
