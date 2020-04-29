---
layout: post
title: linux下文件的打包和压缩
category: practice
---

## 一、文件压缩的原理

​		简单来说，压缩技术就是在存储时将文件里面没有使用的空间利用起来，主要有以下两种情况。

1. 存储时去掉没有用到的空间。假设我们只是存储一个数字 1 ，二进制表示数字1只需1个bit是1就可以表示（1=1*2^0），但是为了满足操作系统对数据的存取要求，只能通过byte的形态来记录。1 byte = 8 bits，所以每个byte当中会有8个bits，而其他的7个bits将自动的被填上0，其实那7个bits是没有用到的。

2. 另外一种压缩技术是将重复的数据进行统计记录。举例来说，如果数据为[1111…]共有100个1，那么记录为“100个1”也能精简文件的大小。

## 二、linux常见的压缩指令

Linux支持的压缩指令可以通过不同的后缀名进行区分，常见的扩展名：

- .zip：zip程序压缩的文件
- .gz：gzip程序压缩的文件
- .bz2：bzip2程序压缩的文件
- .xz：xz程序压缩的文件
- .tar：tar程序打包的数据，并没有压缩过
- .tar.gz：tar程序打包的数据，经过gzip压缩过
- .tar.bz2：tar程序打包的数据，经过bzip2压缩过
- .tar.xz：tar程序打包的文件，经过xz压缩过

## 三、常用实例

以把/etc中内容打包压缩到/tmp/etc.*,然后在解压回/etc为例

### 1、tar命令

```shell
打包：tar -cvf /tmp/etc.tar /etc

解包：tar -xvf /tmp/etc.tar /etc 
```

 常用参数 ：

- **-c** ：建立压缩档案(create 的意思)；

- **-x** ：解压档案！

- **-v** ： 显示所有过程 ，执行过程会打印在屏幕上！ 

- **-f** ： 使用档案名字，这个参数是*最后*一个参数，后面只能接档案名。 

### 2、zip命令

```shell
压缩：zip -r /tmp/etc.zip /etc 

解压：unzip /tmp/etc.zip /etc
```

- **-r**： 将指定的目录下的所有子目录以及文件一起处理

### 3、gz命令

```shell
压缩：tar -czvf /tmp/etc.tar.gz /etc 

解压：tar -xzvf /tmp/etc.tar.gz /etc  
```

- **-z**：使用gzip命令对文件进行压缩或解压缩。

### 4、bz2命令

```shell
压缩：tar -cjvf /tmp/etc.tar.bz2 /etc 

解压：tar -xjvf /tmp/etc.tar.bz2 /etc  
```

- **-j**：使用bz2命令对文件进行压缩或解压缩。

### 5、xz命令（必须分两步）

```shell
打包：tar -cvf /tmp/etc.tar /etc   
压缩：xz -z /tmp/etc.tar #将 /tmp/etc.tar压缩成为 /tmp/etc.tar.xz

解压缩：xz -d etc.tar.xz #将 etc.tar.xz解压成 etc.tar
解包：tar -xvf etc.tar
```

- **-z**：使用xz命令对文件进行压缩操作。

- **-d**：使用xz命令对文件进行解压缩操作。

> 以上命令均可以将**多个文件夹或文件**打包压缩到一个文件中

## 总结：

没有特殊要求**推荐使用**bz2命令，它是**速度**和**压缩比**都比较折中的命令，如果想要快速压缩文件，可以使用**gz命令**，如果追求最高的压缩比，可以使用**xz命令** 。



