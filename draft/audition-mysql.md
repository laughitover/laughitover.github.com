怎么查询慢sql
MySQL提供慢查询日志，开启可能影响性能
long_query_time
show variables like 'long_query_time';
select * from mysql.show_log;

2、mysqlDumpSlow工具 排序 sql

c:访问技术
l:锁定时间
r:返回记录
t:查询时间
al：平均锁定时间
ar：平均返回记录数
at：平均查询时间

数据库隔离级别
                             脏读      不可重复读       幻读
read uncommitted               ✔        ✔               ✔
readcommitted（oracle默认）              ✔               ✔
repeatable read（mysql默认）                             ✔
serializable

读锁 共享锁

写锁
1、表锁
2、行锁
    悲观锁：for update
    乐观锁： 版本号

A:原子性 undo log
C:一致性 bin log
I:隔离性 mvcc
D:持久性 redo log


###索引种类
1、普通索引
2、唯一索引：unique 值必须唯一，允许有控值，如果是组合索引，组合必须唯一
3、主键索引：primary 特殊的唯一索引，不允许有空值
4、组合索引：多个字段上创建的索引
5、全文索引：fulltext 用来查找文本中的关键字
myISAM支持 char，varchar， text 后创建效率高

###索引失效
1、like以%开头，索引失效，以%结尾有效
2、or语句前后没有同事使用索引，索引失效
3、出现数据类型转换，varchar不加''，转换成int
4、做索引上使用is null或者is not null
5、在索引字段使用not,<>,!=, k<>0优化为key>0 or key<0
6、对索引字段进行计算操作，使用函数
7、组合索引没有应用最左前缀，最左前缀与顺序无关
8、全表扫描速度比走索引快 