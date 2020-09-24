---
layout: post
title: Java程序优雅关闭的两种方法
category: interview
---

java程序关闭时，往往需要做一些善后工作，称之为优雅关闭。这里介绍两种比较典型的方法：

## 一、注册关闭钩子
通过调用Runtime.getRuntime().addShutdownHook()方法，添加一个最后运行的线程。
```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
        // 注册关闭钩子
        Runtime.getRuntime().addShutdownHook(new Thread(){
            @Override
            public void run() {
                // 执行收尾工作
                System.out.println("do something on shutdown hook");
            }
        });
    }

}
```
## 二、实现SignalHandler接口

程序启动时实例化一个SignalHandler的实现类，并注册信号。
这样，在进程被kill的时候就会触发KillHandler的handle方法。

### 1、项目启动时注册信号
可以直接在main方法里注册，spring项目也可以在容器初始化的时候注册，这里采用后者。
```java
package com.example.demo.handler;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import sun.misc.Signal;

import javax.annotation.PostConstruct;

@Slf4j
@Component
public class KillManager {

    @Autowired
    private KillHandler killHandler;

    @PostConstruct
    public void registerKillHandler() {
        registerSignal("TERM");
//        killHandler.registerSignal("HUP");
        registerSignal("INT");
//        killHandler.registerSignal("QUIT");
//        killHandler.registerSignal("ILL");
        registerSignal("ABRT");
//        killHandler.registerSignal("BUS");
//        killHandler.registerSignal("KILL");
    }

    private void registerSignal(String signalName) {
        log.info("register signal: {}  ", signalName);
        Signal signal = new Signal(signalName);
        Signal.handle(signal, killHandler);
    }

}
```
### 2、实现SignalHandler接口

```java
package com.example.demo.handler;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import sun.misc.Signal;
import sun.misc.SignalHandler;

@Slf4j
@Service
public class KillHandler implements SignalHandler {

    /**
     * 强制停止服务，用于程序主动停止
     */
    public void forceShutdownServer() {
        log.error(" KillHandler force to shutdown server !!!! ");
        Signal signal = new Signal("TERM");
        handle(signal);
    }

    @Override
    public void handle(Signal signal) {
        log.info("server get signal to stop server : {}  {}", signal);
        if (signal.getName().equals("TERM")) {
            stopServer();
        }
        if (signal.getName().equals("INT") || signal.getName().equals("HUP")) {
            System.out.println("server get signal"+signal.getName());
        }
    }

    /**
     * 停止服务，可以做一些善后工作，这里主动注销eurekaClient
     */
    private void stopServer() {
        //改变服务运行状态，需要自己设置状态
        Application.setRunningStatus(false);
        try {
            eurekaAutoServiceRegistration.stop();
            if (client != null) {
                client.shutdown();
                client = null;
            }
            DiscoveryManager.getInstance().shutdownComponent();
            Thread.sleep(2000);
        } catch (Exception e) {
            log.error(" KillHandler handle sleep current exception ", e);
        }
        log.info("KillHandler instance start to stop server!!! ");
        Runtime.getRuntime().exit(0);
    }

}
```


## 三、 区别

第一种方法在进程被kill的时候main函数就已经结束了，仅会运行shutdownHook中run()方法的代码。
第二种方法handle函数会在进程被kill时收到信号，对main函数的运行不会有影响，
我们可以自己在main函数中添加状态，当收到信号时修改状态，根据状态灵活处理程序。

## 补充
kill -15 pid 表示程序结束(terminate)信号，相当于idea 点击一下stop按钮
kill -9 pid  表示强制停止进程，用来立即结束程序，相当于第二下点击stop按钮