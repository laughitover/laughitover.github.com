---
layout: post
title: docker教程（二）windows安装docker
category: interview
---

1、windows安装docker

### 配置镜像加速器

 在系统右下角托盘图标内右键菜单选择 `Settings`，打开配置窗口后左侧导航菜单选择 `Docker Daemon`。编辑窗口内的JSON串，填写下方加速器地址： 

```json
{
  "registry-mirrors": ["https://*****.mirror.aliyuncs.com"]
}
```

注意： `Docker for Windows` 有两种运行模式，一种运行`Windows`相关容器，一种运行传统的`Linux`容器。同一时间只能选择一种模式运行。 

2、docker配置远程访问 

 打开`docker`的设置界面，在`General`里面找到`Expose daemon on tcp://localhost:2375 without TLS`选项，打上勾，即可进行本地或远程API调用。 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\001.png)

3、配置idea连接docker服务

 打开`settings`可以看到`docker` ， 点击`+`，添加一个连接。 我们通过tcp的方式连接到`linux`或者`windows`的`docker`服务。信息配置好后，`idea`会自动检测。然后会输出`connection successful`。 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\003.png)

4、制作image 运行容器中项目

 `docker`启动项 

 选择编辑： 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\006.png)

 添加一个启动项： 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\005.png)

 填写`docker`相关的参数： 



![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\004.png)

最终的docker命令

`docker build -f dockerfile.xxl-job.local -t xxl-job . && docker run -p 8080:8080 --name xxl-job xxl-job `

经过image制作和运行， 最终容器启动完成 

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\007.png)

5、调试容器中的项目

启动参数中添加以下

```json
-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=9998 
```

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\008.png)

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\009.png)

![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\010.png)

![011](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\011.png)

![012](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\012.png)



![](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\011.png)

![012](D:\workspace\laughitover.github.com\assets\images\2020\dockerDebug\012.png)







 https://www.jianshu.com/p/0dcc2e43963b 



docker没启动报错  daemon 