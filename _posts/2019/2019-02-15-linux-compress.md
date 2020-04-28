---
layout: post
title: linux下文件的打包和压缩
category: practice
---

```shell
重点内容：
    tar命令
    zip命令
    gz命令 
    bz2命令 
    xz命令
```

### 一、文件压缩的原理

​		简单来说，压缩技术就是在存储时将文件里面没有使用的空间利用起来，主要有以下两种情况。

1. 存储时去掉没有用到的空间。假设我们只是存储一个数字 1 ，二进制表示数字1只需1个bit是1就可以表示，但是为了满足操作系统对数据的存取要求，只能通过byte的形态来记录。1 byte = 8 bits，所以每个byte当中会有8个bits，其他的7个bits将自动的被填上0，其实那7个bits应该是空的才对，
2. 另外一种压缩技术是将重复的数据进行统计记录。举例来说，如果数据为[1111…]共有100个1，那么记录为“100个1”也能够精简文件的大小。

### 二、linux常见的压缩指令

Linux支持的压缩指令可以通过不同的后缀名进行区分，常见的扩展名：

- .zip：zip程序压缩的文件
- .gz：gzip程序压缩的文件
- .bz2：bzip2程序压缩的文件
- .xz：xz程序压缩的文件
- .tar：tar程序打包的数据，并没有压缩过
- .tar.gz：tar程序打包的数据，经过gzip压缩过
- .tar.bz2：tar程序打包的数据，经过bzip2压缩过
- .tar.xz：tar程序打包的文件，经过xz压缩过

### 三、常用实例

以把/etc中内容打包压缩到/tmp/etc.*,然后在解压回/etc为例

1. tar命令

   ```shell
   打包：tar -cvf /tmp/etc.tar /etc
   
   解包：tar -xvf /tmp/etc.tar /etc 
   ```

    常用参数 ：

   **-c** ：建立一个压缩档案的参数指令(create 的意思)；

   **-x** ：解开一个压缩档案的参数指令！

   **-v** ：压缩的过程中显示档案！这个常用，但不建议用在背景执行过程！ 

   **-f** ：使用档名，请留意，在 f 之后要立即接档名喔！不要再加参数！ 

2. zip命令

   ```shell
   压缩：zip -r /tmp/etc.zip /etc 
   
   解压：unzip /tmp/etc.zip /etc
   ```

3. gz命令 

   ```shell
   压缩：tar -czvf /tmp/etc.tar.gz /etc 
   
   解压：tar -xzvf /tmp/etc.tar.gz /etc  
   ```

   - -t：可以用来检验一个压缩文件的一致性，看看文件有没有错误
   - -v：可以显示出原文件/压缩文件的压缩比等信息

4.  bz2命令 

   ```shell
   压缩：tar -cjvf /tmp/etc.tar.bz2 /etc 
   
   解压：tar -xjvf /tmp/etc.tar.bz2 /etc  
   ```
   
5. xz命令（必须分两步）

   ```shell
   打包：tar -cvf /tmp/etc.tar /etc   
   压缩：xz -z /tmp/etc.tar #将 /tmp/etc.tar压缩成为 /tmp/etc.tar.xz
   
   解压缩：xz -d etc.tar.xz #将 etc.tar.xz解压成 etc.tar
   解包：tar -xvf etc.tar
   ```

> 以上命令均可以将多个文件夹或文件打包压缩到一个文件中

#### 总结：

想要最快速度压缩文件建议使用**gz命令**，想要最高的压缩比**xz命令**是最好的选择。**bz2是速度和压缩比折中的命令，推荐使用** 。



