go面试知识点总结：
## 项目相关：
介绍项目的流程：
1. 能说的通业务流程
2. 用的技术栈是什么，如何使用
3. 关注的底层实现
4. 碰到的问题是什么（场景、任务、行动、结果）// 话术
5.

## Go技术相关（做什么的，怎么做，为什么这样做）
1. 引用类型有哪些？
2. 数组和切片的区别？（数组定义后cap不能再改变）
3. 切片的底层实现？
   1. 扩增算法
4. map的底层实现？（解决hash碰撞的方法）
   1. map并发安全吗？
5. channel的用法（使用通信进行共享内存），channel是如何使用的？
   1. 有无缓存的channel的区别
   2. channel的底层实现
6. 锁的用法，实现原理，锁有哪些类，使用场景分别是什么？
   1. 锁的饥饿模式和自旋锁
7. make和new的区别？
8. sync中context的用法？
9. sync中waitGroup和map的使用？
   1. sync.map的缺点
10. 空的struct{}有什么作用？
11. interface{}的用法，断言的使用？
12. go协程和线程的区别？
13. GMP调度模型 g0 m0 全局队列
    1. 全局队列、本地队列、调度原理
    2. gmp中的阻塞有哪些
14. GC的实现
    1. 什么时候发生回收
    2. 
15. go的内存区间介绍
    1. 内存泄漏的情况
16. 协程池怎么实现

## Linux相关
1. linux内存区间
2. 查看进程/端口/ip/mem的命令
   1. 查看僵尸进程的命令（僵尸进程、孤儿进程是什么）
   2. dns查看ip的命令
3. 

## Mysql
1. 索引及其优化？
2. 引擎有哪些？有什么区别
3. 锁
4. 事务以及事务的等级？
5. 事务造成的影响？如果避免这种情况？（脏读、幻读、不可重复读）
6.

## Redis
1. 几种结构
2. 基本介绍，为什么会快？
3. 底层实现是什么
4. 秒杀场景的使用？
5. 缓存击穿、缓存穿透、缓存雪崩

## 网络协议
1. TCP/IP协议，iso7层协议或TCP4层协议
2. HTTP1.0与HTTP1.1以及HTTP2.0的区别
3. 三次握手、四次挥手的原因，为什么，半连接状态？
4. 

## 中间件
了解一些中间件的使用及原理：
1. MQ的分类和选型
2. Kafka
3. RabbitMQ


## 算法
基本的算法题目

## 微服务
1. 什么是微服务 
2. 怎么划分服务（DDD那一套） 
3. 服务治理（包括但不限于：限流，熔断、负载、监控、鉴权、服务发现和注册等等） 
4. rpc 
5. protobuf 编码为什么这么高效？和 json 比较呢？ 
6. gateway

## docker
1. CMD和ENTRYPOINT的区别

## k8s
1. 一个deployment如果部署多个实例pod？怎么实现的
2. 

## 业务
1. 分布式定时任务的设计
2. 状态机的缺点
