---
layout: post
title: 在Docker中缓存maven依赖项
category: interview
---


docker构建spring项目镜像时，使用maven打包，需要下载依赖，受网速和依赖大小限制，有时打包时间过长，
这种情况可以通过上传maven依赖项来解决，
1、设置maven仓库为地址mvn
2、修改settings文件中的maven仓库地址
3、reimport完成之后，把mvn压缩成mvn.tar.bz
4、把mvn.tar.bz放到项目中，随代码一起上传
5、编写Dockerfile，把mvn.tar.bz解压到settings-docker.xml中localRepository标签配置的地址中