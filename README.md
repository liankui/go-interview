go面试知识点总结：
## 项目相关：
介绍项目的流程：
1. 能说的通业务流程
2. 用的技术栈是什么，如何使用
3. 关注的底层实现
4. 碰到的问题是什么（场景、任务、行动、结果）// 话术
5. 提问：针对我的技术上面，有什么好的建议或者意见

## Go技术相关（做什么的，怎么做，为什么这样做）
1. 引用类型有哪些？
2. 数组和切片的区别？（数组定义后cap不能再改变）
3. 切片的底层实现？
   1. 扩增算法
   2. for range 的时候它的地址会发生变化么
4. defer，多个 defer 的顺序，defer 在什么时机会修改返回值？
   1. defer recover
5. uint 类型溢出
   1. 介绍 rune 类型
6. map的底层实现？（哈希查找表，使用链表解决哈希冲突）
   1. map并发安全吗？解决办法
   2. map 中删除一个 key，它的内存会释放么
   3. map 取一个 key，然后修改这个值，原 map 数据的值会不会变化 
   4. nil map 和空 map 有何不同
   5. map承载量有多大，什么情况下扩容，怎么扩容
   6. sync.Map怎么实现线程安全的，怎么实现的
   7. sync.Map的优缺点和使用场景
7. channel的用法（使用通信进行共享内存），channel是如何使用的？
   1. 有无缓存的channel的区别
   2. channel的底层实现
   3. 向 channel 发送数据和从 channel 读数据的流程会怎么样
8. 锁的用法，实现原理，锁有哪些类，使用场景分别是什么？
   1. Mutex 是悲观锁还是乐观锁？悲观锁、乐观锁是什么
   2. Mutex 有几种模式
   3. 锁的饥饿模式和自旋锁
   4. 读写锁底层是怎么实现的
9. make和new的区别？
10. sync中context的用法？
    1. 有哪些函数，使用场景
11. sync中waitGroup的使用？
12. sync.Pool的使用场景？不同版本的变化1.12->1.13
13. 空的struct{}有什么作用？
14. interface{}的用法，断言的使用？
15. 反射原理
    1. 解析tag怎么实现
16. go协程和线程的区别？（进程、线程、协程）
17. GMP调度模型 g0 m0 全局队列
    1. 全局队列、本地队列、调度原理
    2. gmp中的阻塞有哪些
    3. 各个调度模型的版本及解决的问题
    4. 
18. GC的实现
    1. 什么时候发生回收
    2. gc中stw的时机
    3. 各个阶段是如何解决的
19. go的内存区间
    1. 内存管理 Channel 分配在栈上还是堆上？哪些对象分配在堆上，哪些对象分配在栈上？
    2. 大对象小对象，为什么小对象多了会造成 gc 压力？
    3. 堆和栈的区别？
    4. 内存泄露，什么情况下内存会泄露？怎么定位排查内存泄漏问题？怎么处理
    5. golang 的内存逃逸吗？什么情况下会发生内存逃逸？
    6. 如何手动对其内存
    7. 栈扩容和栈缩容，连续栈的缺点 https://segmentfault.com/a/1190000019570427
20. 协程池怎么实现
21. 主协程如何等其余协程完再操作
22. 利用 golang 特性，设计一个 QPS 为 500 的服务器
23. var _ io.Writer = (*myWriter)(nil) 写法的原因 （定义为nil的myWriter指针类型的变量使用_进行接收）
24. 问题排查 pprof trace

## Linux相关
1. linux内存区间
2. 僵尸进程、孤儿进程
3. 查看进程/端口/ip/mem/硬盘的命令
   1. 查看僵尸进程的命令
   2. dns查看ip的命令
4. 

## Mysql
1. 索引？
   1. 索引创建原则（什么情况下用和不用）
   2. 索引的数据结构，b+树和一般二叉树、平衡二叉树、hash树、b树的区别
   3. 索引类型
   4. 聚集索引和非聚集索引的区别
   5. 联合索引，最有原则
2. 引擎有哪些？有什么区别
3. 事务以及事务的等级？
   1. 事务的特性、特性实现的原理
   2. 事务隔离级别，各个隔离级别解决的问题
   3. MVCC的原理
4. 锁
   1. 行锁，表锁，悲观锁啊，乐观锁啊，排它锁，共享锁，间隙锁，范围锁，临键锁，两阶段锁，死锁的原因
   2. 事务中各个隔离级别用的锁的类型已经怎么用的锁
   3. 分锁力度和锁算法（间隙锁、记录锁、临检锁），什么时候会用到
5. 数据结构
   1. varchar和char的区别
   2. int和int(11)区别
   3. tinyint和int区别
6. 日志有哪几种
   1. binlog日志种类，优缺点，一般怎么设置
   2. binlog日志格式选择
   3. redo log、undo log 的使用
7. 高可用怎么部署（主从部署）
   1. 读写分离原理
   2. 主从同步慢如何解决
   3. 大批量删除数据，怎么快速释放内存
   4. delete会立即释放内存吗
   5. truncate和drop
8. 优化方式 分库分表规则，出现的问题 如何处理
9. 慢查询 数据量大，查询慢，怎么解决的（执行计划、哪些参数）
10. 正在运行的业务中，数据表修改字段如何避免锁表导致业务不可用的问题。

## Redis
1. 几种数据结构
   1. 使用场景
   2. 各种数据类型的底层数据结构
   3. zset底层 跳跃表(skiplist)和链表，skiplist为什么这么快，两种编码方式，如何配置
2. 基本介绍，为什么会快？
   1. 线程模型，哪里单线程，哪里多线程
3. 内存持久化的方式，优缺点，如何选择？混合持久化
4. 内存淘汰策略
5. 事务和管道 区别不太大，一个是在客户端打包，一个是在服务端打包
6. 集群方案 主从、哨兵、分片（客户端分片和服务端分片）
7. 缓存异常解决：缓存击穿、缓存穿透、缓存雪崩及解决方案
8. 缓存一致性 如何保证redis和db数据一致性
9. 分布式锁的实现，redis的lock和stream能不能作为mq， 
10. redis怎么实现延时队列、实现异步队列，
11. 布隆过滤器
12. 排行榜，用zset，根据积分来排序，积分高的时间近的排前面，怎么实现

## 网络协议
1. TCP/IP协议，iso7层协议或TCP4层协议
2. HTTP1.0与HTTP1.1以及HTTP2.0的区别
3. 三次握手、四次挥手的原因，为什么，
   1. 半连接状态time_wait？
   2. 滑动窗口
   3. nginx通信
4. 浏览器输入地址，中间发生的过程，仔细的描述一下
5. 

## 中间件
了解一些中间件的使用及原理：
1. MQ的分类和选型
2. Kafka 整体架构
   1. 分区的作用，分区的实现是怎么实现的？
   2. 分区器、序列化器、拦截器了解不？
   3. 重复消费和漏消费，客户端和服务端都是怎么解决的？
   4. kafka 为什么性能这么好，怎么实现的
   5. kafka集群
3. RabbitMQ


## 算法
基本的算法题目

## 微服务
1. 什么是微服务 
   1. 为什么使用微服务框架
2. 怎么划分服务（DDD那一套） 
3. 服务治理（包括但不限于：限流，熔断、负载、监控、鉴权、服务发现和注册等等） 
4. rpc 
5. protobuf 编码为什么这么高效？和 json 比较呢？ 
6. gateway

## docker
1. CMD和ENTRYPOINT的区别

## k8s
1. 一个deployment如果部署多个实例pod？怎么实现的 （控制循环）
2. 

## 业务
1. 分布式定时任务的设计
2. 状态机的缺点
