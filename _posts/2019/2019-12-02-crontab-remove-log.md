---
layout: post
title: linux定时删除日志文件
category: practice
---

linux服务器上的日志文件需要定时清理，可以使用crontab定时执行脚本进行删除。

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/crontab/001.png)

## 一、删除文件

### 命令

```
find 对应目录 -mtime +天数 -name "文件名" -exec rm -rf {} \;
```

### 实例

将/data/prod/kafka_connect_tongji/logs/out/目录下所有10天前带".log"的文件删除。

1. 编写脚本: vim auto-del-log.sh

```
#!/bin/sh

days=9
echo "执行清理["$days+1"]前日志任务，开始时间：$(date "+%Y-%m-%d %H:%M:%S")"
find /data/prod/kafka_connect_tongji/logs/out/ -mtime +$days -name "*.log.*" -exec rm -rf {} \;
```

2. 赋可执行权限: chmod +x auto-del-log.sh

3. 执行: sh auto-del-log.sh

## 二、清空文件

1. 编写脚本: vim clearLog.sh

```
#!/bin/bash

echo $(date "+%Y-%m-%d %H:%M:%S")
status(){
  find /data/prod/test/ -name '*.out' -type f -print -exec truncate -s 0 {} \;
  echo "清空log成功"
  return
}

case "$1" in 
    status)
        status
        ;;
    *)
        echo "清除失败"
        ;;
esac
```
2. 赋可执行权限: chmod +x clearLog.sh

3. 执行 `sh clearLog.sh status`

## 计划任务

手动执行语句太麻烦，可以将这小语句写到一个可执行shell脚本文件中，再设置crond调度执行，那就可以让系统自动去清理相关文件。


- 查看crond服务状态

```shell script
service crond status
```

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/crontab/002.png)

- 编写crond,将auto-del-log.sh执行脚本加入到系统计划任务，到点自动执行

`crontab -e`


`10 0 * * * cd /data/prod/kafka_connect_tongji/logs; ./auto-del-log.sh >> auto.log 2>&1`

> 这里的设置是每天凌晨0点10分执行auto-del-log.sh文件进行数据清理了,日志输出到auto.log。

- 查看当前crond 任务

`crontab -l`

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/crontab/003.png)



