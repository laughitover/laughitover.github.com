---
layout: post
title: docker教程（一）windows系统安装docker
category: interview
---



### 一、下载安装 Docker for Windows 

 Docker Desktop下载地址：http://mirrors.aliyun.com/docker-toolbox/windows/docker-for-windows/ 

成功安装Docker Desktop后，cmd中（已安装git可以在Git bash中）输入docker --version，打印出版本信息，表示Docker for Windows安装成功。

### 二、docker简单使用实例

#### 1、下载示例项目

通过命令行终端（cmd）或者Git，从GitHub克隆示例项目：

```shell
git clone https://github.com/dockersamples/node-bulletin-bo
cd node-bulletin-board/bulletin-board-app
```

#### 2、构建镜像

确保当前目录是`node-bulletin-board/bulletin-board-app`。运行以下命令来构建公告板image：

```script
docker build --tag bulletinboard:1.0 .
```

将看到Docker逐步完成Dockerfile中的每条指令，逐步构建image。如果成功，最后会打印出`Successfully tagged bulletinboard:1.0`。

>  1、`Dockerfile`是一个文本文档，Docker可以通过`Dockerfile`来自动构建映像 。实际`Dockerfile`就是一组命令集合。 
>
> 2、忽略最后打印出 SECURITY WARNING”（安全警告）的消息。

#### 3、将图像作为容器运行

1. 运行以下命令基于新镜像启动容器：

   ```script
   docker run --publish 8000:8080 --detach --name bb bulletinboard:1.0
   ```

   - `--publish`Docker将主机端口8000映射到容器的端口8080。
- `--detach` Docker在后台运行此容器。
   - `--name`指定一个名称，在后续命令中，您可以使用该名称来引用您的容器`bb`。
   
2. 在浏览器中输入`localhost:8000`。应该看到公告板应用程序已启动并正在运行。

   ![](D:\workspace\laughitover.github.com\assets\images\2020\dcoker windows\004.png)

3. 删除命令：

   ```script
   docker rm --force bb
   ```

   > `--force`停止正在运行的容器，因此可以将其删除。也可以先停止运行该容器`docker stop bb`，则无需使用`--force`来删除。



### 三、在Docker Hub上共享镜像

类似于我们可以把代码放到代码托管服务平台Git Hub，我们也可以把自己的 Docker镜像放到基于云的镜像存储库 Docker Hub。这样就可以在任何计算机上运行它们。

#### 1、设置Docker Hub帐户

- 访问[Docker Hub注册](https://hub.docker.com/signup)页面。

- 填写注册信息创建Docker ID。

- 电子邮件验证。

- 点击工具栏或系统托盘中的Docker图标，然后点击**登录/创建Docker ID**。

- 填写新的Docker ID和密码。成功通过身份验证后，Docker ID将显示在Docker Desktop菜单中，代替刚使用时看到的“登录”选项。

> 也可以通过输入`docker login`命令登录Docker Hub 。

#### 2、创建一个Docker Hub存储库并push image

确保已设置Docker Hub帐户并将其连接到Docker桌面，创建第一个存储库，并将公告板镜像推送到Docker Hub。

- 单击菜单栏中的Docker图标，然后导航至**存储库>创建**。您将被重定向到Docker Hub上的**Create Repository**页面。

- 输入存储库名称为`bulletinboard`，然后单击页面底部的**创建**。

![进行回购](https://docs.docker.com/get-started/images/newrepo.png)

- 现在可以在Docker Hub上共享镜像了，但是，您必须首先做一件事：必须*正确命名*镜像的*名称*才能在Docker Hub上共享。具体来说，您必须将图像命名为`/:`。

确保您位于`node-bulletin-board/bulletin-board-app`终端或PowerShell 中的目录中，然后运行：

```shell
docker tag bulletinboard:1.0 <Your Docker ID>/bulletinboard:1.0
```

- 最后，将映像推送到Docker Hub：

```
docker push <Your Docker ID>/bulletinboard:1.0
```

在[Docker Hub中](https://hub.docker.com/repositories)访问存储库，将在此处看到新镜像。注意，默认情况下，Docker Hub存储库是公共的。

### 结论

至此，镜像已在Docker Hub上可用，可以在任何地方运行它。如果尝试在尚未安装该镜像的机器上使用它，则Docker将自动尝试从Docker Hub下载它。通过以这种方式移动镜像，不再需要在计算机上安装除Docker以外的任何依赖项。容器化应用程序的依赖关系已完全封装并隔离在镜像中，通过Docker Hub已经完全实现镜像共享。

此外，还可以在Docker Hub存储库描述中添加包含Dockerfile的源代码的链接或注释。





### 配置远程访问

打开`docker`的设置界面，找到`General`,在里面找到`Expose daemon on tcp://localhost:2375 without TLS`选项，打上勾，即可进行本地或远程API调用。

![img](https:////upload-images.jianshu.io/upload_images/9264166-0b03a9b7618a678a.png?imageMogr2/auto-orient/strip|imageView2/2/w/812/format/webp)

idea安装docker插件

### 配置连接

重启完成后，我们打开`settings`可以看到`docker`

![img](https:////upload-images.jianshu.io/upload_images/9264166-f23824f0276df35f.png?imageMogr2/auto-orient/strip|imageView2/2/w/1152/format/webp)

4.png

点击`+`，添加一个连接。

![img](https:////upload-images.jianshu.io/upload_images/9264166-0b7c809d4afb11db.png?imageMogr2/auto-orient/strip|imageView2/2/w/1159/format/webp)

5.png

我们通过tcp的方式连接到`linux`或者`windows`的`docker`服务。信息配置好后，`idea`会自动检测。然后会输出`connection successful`。

## idea上运行docker项目

然后配置`docker`启动项
选择编辑：

![img](https://upload-images.jianshu.io/upload_images/9264166-6426456d6f0d941d.png?imageMogr2/auto-orient/strip|imageView2/2/w/395/format/webp)

12.png

添加一个启动项：

![img](https://upload-images.jianshu.io/upload_images/9264166-b9e3ad840b0fbe64.png?imageMogr2/auto-orient/strip|imageView2/2/w/490/format/webp)

![1592646208038](C:\Users\wangjs5\AppData\Roaming\Typora\typora-user-images\1592646208038.png)

