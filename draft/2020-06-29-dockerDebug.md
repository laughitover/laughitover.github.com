---
layout: post
title: docker教程（二）idea通过docker部署并调试SpringBoot项目
category: interview
---



2、docker配置远程访问 

 

## 一、使用idea在docker中构建并运行springboot项目

### 1、docker配置远程访问 

打开`docker`的设置界面，在`General`里面找到`Expose daemon on tcp://localhost:2375 without TLS`选项，打上勾，即可进行本地或远程API调用。 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\001.png)

### 2、配置idea连接docker服务

 打开`settings`可以看到`docker` ， 点击`+`，添加一个连接。 我们通过tcp的方式连接到`linux`或者`windows`的`docker`服务。信息配置好后，`idea`会自动检测。然后会输出`connection successful`。 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\003.png)

### 3、制作image并运行容器

 `docker`启动项，选择编辑： 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\006.png)

 添加一个docker启动项： 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\005.png)

 填写`docker`相关的参数： 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\004.png)

最终得到docker命令

`docker build -f dockerfile.xxl-job.local -t xxl-job . && docker run -p 8080:8080 --name xxl-job xxl-job `

经过image制作和运行， 最终容器启动完成 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\007.png)

### 二、调试容器中的项目

启动参数中添加以下

```json
-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=9998 
```



添加debug项：

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\008.png)

配置Remote：

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\009.png)

编辑容器：

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\010.png)

添加端口映射：

![011](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\011.png)

重新发布容器：

![012](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\012.png)

可以像本地运行的项目一样对项目进行调试：

<img src="D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\013.png" style="zoom:60%;" />



 https://www.jianshu.com/p/0dcc2e43963b 



docker没启动报错  daemon 