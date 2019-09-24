---
layout: post
title: java程序中的典型错误
category: practice
---

### IDEA报错：Error running $classname: Command line is too long. Shorten command line for $classname.or also for Spring Boot default configuration

原因：大多数操作系统都有命令行长度限制。如果类路径太长，或者有许多VM参数，程序就无法启动。
解决方法：
方法一、
修改idea运行参数，shorten command line 提供三种选项缩短类路径。
1. none：这是默认选项，idea不会缩短命令行。如果命令行超出了OS限制，这个想法将无法运行您的应用程序，但是工具提示将建议配置缩短器。
2. JAR manifest：idea 通过临时的classpath.jar传递长的类路径。原始类路径在MANIFEST.MF中定义为classpath.jar中的类路径属性。
3. classpath file：idea 将一个长类路径写入文本文件中。
建议使用第三种

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/typicalError/0501.png)

方法二
修改项目下.idea\workspace.xml.找到标签 <component name="PropertiesComponent"> ， 
在标签里加一行  <property name="dynamic.classpath" value="true" />

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/typicalError/0502.png)

>[官方文档](https://blog.jetbrains.com/idea/2017/10/intellij-idea-2017-3-eap-configurable-command-line-shortener-and-more/)