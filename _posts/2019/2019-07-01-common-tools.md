---
layout: post
title: 开发常用工具
category: introduce
---

##  Postman
　　在我们平时开发中，写完功能之后肯定要进行测试，java程序的单元测试已经非常好用，那么web请求如何模拟测试呢？
这就需要有一个比较给力的Http请求模拟工具，现在流行的这种工具也挺多的，像火狐浏览器插件-RESTClient，Chrome浏览器插件-Postman等等。
这里主要介绍一下Postman。 
### 一、下载安装
>官网下载地址:https://www.getpostman.com/downloads/

Postman的安装非常简单，在windows系统只需要双击.exe文件，然后什么都不需要操作，它直接就自己完成了，

## 二、基本使用方法

Postman的主界面：
![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/commonTools/01.png)
介绍：
- 1、url地址栏。可选get和post，get请求很简单直接点击send可以得到返回的结果，post请求操作需要添加参数
- 2、Body下填写post请求需要的json参数
- 3、选择raw
- 4、header参数，以键值的方式添加，如：json格式的提交数据需要添加：```Content-Type application/json```
- 5、如果请求需要认证可以在此进行设置
- 6、返回的结果
- 7、可以把常用的请求分类存起来

>更多操作见官网：https://www.getpostman.com