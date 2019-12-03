---
layout: post
title: linux定时删除日志文件
category: practice
---

linux服务器上的日志文件需要定时清理，可以使用crontab定时执行脚本进行删除。

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/crontab/001.png)

## 删除文件

### 命令

```
find 对应目录 -mtime +天数 -name "文件名" -exec rm -rf {} \;
```

### 实例

将/data/prod/kafka_connect_tongji/logs/out/目录下所有10天前带".log"的文件删除。

```
 find /data/prod/kafka_connect_tongji/logs/out/ -mtime +10 -name "*.log.*" -exec rm -rf {} \;
```


## 计划任务

手动执行语句太麻烦，可以将这小语句写到一个可执行shell脚本文件中，再设置crond调度执行，那就可以让系统自动去清理相关文件。

### 编写shell脚本

- 新建auto-del-10-days-ago-log.sh

```
touch /data/prod/kafka_connect_tongji/logs/auto-del-10-days-ago-log.sh
```

- 编辑shell脚本：

编辑auto-del-10-days-ago-log.sh文件

vim auto-del-10-days-ago-log.sh

```
#!/bin/sh

find /opt/soft/log/ -mtime +30 -name "*.log" -exec rm -rf {} \;
```

保存退出(:wq)。

 
### 计划任务

- 查看crond服务状态

```shell script
service crond status
```

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/crontab/002.png)

- 编写crond

`crontab -e`

将auto-del-10-days-ago-log.sh执行脚本加入到系统计划任务，到点自动执行

输入：

10 0 * * * /opt/soft/log/auto-del-10-days-ago-log.sh

>这里的设置是每天凌晨0点10分执行auto-del-10-days-ago-log.sh文件进行数据清理任务了。

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/crontab/003.png)

- 查看当前crond 任务

`crontab -l`

![在这里插入图片描述](http://www.laughitover.com/assets/images/2019/crontab/004.png)



