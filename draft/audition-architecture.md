限流算法
1、滑动窗口(计数器算法)
2、令牌桶算法
3、漏桶算法

spring.factories

dubbo路由策略
1、RandomLoadBalance：随机负载（默认）
2、RoundRobinLoadBalance：轮询
3、leastActiveLoadBalance：最少活跃调用
4、ConsisHashLoadBalance：一致性hash，相同参数的请求总是落在同一台机器上