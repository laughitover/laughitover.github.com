auto.offset.reset
1、earliest：各分区已经提交的offset，从提交的offset开始消费，无提交的offset，从头开始消费
2、latest：各分区已经提交的offset，从提交的offset开始消费，无提交的offset，消费新产生的
3、none：各分区已经提交的offset，从提交的offset开始消费，有一个未提交，抛异常

ISR in-sync replicas

ack
0：发出去
1：leader成功落盘
all：所有副本都成功

###选举  raft 选举算法
1、控制器选举
broker id
leader 选举 在zookeeper创建/controller
follower 防止脑裂 watch epoch
2、分区副本选择
1、首领副本（leader）
2、跟随着副本：不处理客户端请求
3、首选首领副本：创建分区时指定，默认分区的第一个
默认10s 同步副本才可选举