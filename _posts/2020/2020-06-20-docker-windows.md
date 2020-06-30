---
layout: post
title: docker教程（一）windows系统安装docker
category: interview
---


## 一、下载安装 Docker for Windows 

下载地址：[Docker Desktop](http://mirrors.aliyun.com/docker-toolbox/windows/docker-for-windows/)，
成功安装Docker Desktop后，cmd中（已安装git可以在Git bash中）输入docker --version，
打印出版本信息，表示Docker for Windows安装成功。
![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/dcokerWindows/002.png)

## 二、docker简单使用实例

### 1、下载示例项目

通过命令行终端（cmd）或者Git，从GitHub克隆示例项目：

```shell
git clone https://github.com/dockersamples/node-bulletin-board
cd node-bulletin-board/bulletin-board-app
```

### 2、构建镜像

确保当前目录是`node-bulletin-board/bulletin-board-app`。运行以下命令来构建公告板image：

```shell
docker build --tag bulletinboard:1.0 .
```

将看到Docker逐步完成Dockerfile中的每条指令，逐步构建image。如果成功，最后会打印出`Successfully tagged bulletinboard:1.0`。

>  1、`Dockerfile`是一个文本文档，Docker可以通过`Dockerfile`来自动构建映像 。实际`Dockerfile`就是一组命令集合。 
>
> 2、忽略最后打印出 SECURITY WARNING”（安全警告）的消息。

### 3、将镜像作为容器运行

1. 运行以下命令基于新镜像启动容器：

   ```shell
   docker run --publish 8000:8080 --detach --name bb bulletinboard:1.0
   ```

   - `--publish`Docker将主机端口8000映射到容器的端口8080。
- `--detach` Docker在后台运行此容器。
   - `--name`指定一个名称，在后续命令中，您可以使用该名称来引用您的容器`bb`。
   
2. 在浏览器中输入`localhost:8000`。应该看到公告板应用程序已启动并正在运行。

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/dcokerWindows/004.png)

3. 删除命令：

   ```shell
   docker rm --force bb
   ```

   > `--force`停止正在运行的容器，因此可以将其删除。也可以先停止运行该容器`docker stop bb`，则无需使用`--force`来删除。



## 三、在Docker Hub上共享镜像

类似于我们可以把代码放到代码托管服务平台Git Hub，我们也可以把自己的 Docker镜像放到基于云的镜像存储库 Docker Hub。这样就可以在任何计算机上运行它们。

### 1、设置Docker Hub帐户

- 访问[Docker Hub注册](https://hub.docker.com/signup)页面。

- 填写注册信息创建Docker ID。

- 电子邮件验证。

- 点击工具栏或系统托盘中的Docker图标，然后点击**登录/创建Docker ID**。

- 填写新的Docker ID和密码。成功通过身份验证后，Docker ID将显示在Docker Desktop菜单中，代替刚使用时看到的“登录”选项。

> 也可以通过输入`docker login`命令登录Docker Hub 。

### 2、创建一个Docker Hub存储库并push image

确保已设置Docker Hub帐户并将其连接到Docker桌面，创建第一个存储库，并将公告板镜像推送到Docker Hub。

- 单击菜单栏中的Docker图标，然后导航至**存储库>创建**。您将被重定向到Docker Hub上的**Create Repository**页面。

- 输入存储库名称为`bulletinboard`，然后单击页面底部的**创建**。

![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/dcokerWindows/005.png)

- 现在可以在Docker Hub上共享镜像了，确保位于目录`node-bulletin-board/bulletin-board-app`中，然后运行：

```shell
docker tag bulletinboard:1.0 <Your Docker ID>/bulletinboard:1.0
```

- 最后，将映像推送到Docker Hub：

```
docker push <Your Docker ID>/bulletinboard:1.0
```

在[Docker Hub中](https://hub.docker.com/repositories)访问存储库，将在此处看到新镜像。注意，默认情况下，Docker Hub存储库是公共的。

## 结论

至此，镜像已经上传到Docker Hub，如果尝试在尚未安装该镜像的机器上使用它，则Docker将自动尝试从Docker Hub下载它。
通过以这种方式，不再需要在计算机上安装除Docker以外的任何依赖项。容器化应用程序的依赖关系已完全封装并隔离在镜像中，
并且通过Docker Hub已经完全实现镜像共享。

## 配置镜像加速器

docker官方镜像仓库地址为：https://hub.docker.com/search?q=&type=image，因为是国外地址，因此下载镜像时速度很慢。
我们可以配置国内镜像加速，在系统右下角托盘图标内右键菜单选择 `Settings`，打开配置窗口后左侧导航菜单选择 `Docker Daemon`。
编辑窗口内的JSON串，填写下方加速器地址： 

```json
{
  "registry-mirrors": ["https://*****.mirror.aliyuncs.com"]
}
```
![在这里插入图片描述](http://www.laughitover.com/assets/images/2020/dcokerWindows/006.png)
